---
layout: post
title: "[Android] 커스텀 다이얼로그(Custom dialog) 만들기 - AlertDialog"
category: Android
tags: [Android, Dialog, Custom, AlertDialog]
comments: true
---

다이얼로그(Dialog)는 사용자에게 추가 정보의 입력이나 내용 확인을 위한 작은 창으로, 사용자가 계속 진행하기 위해 필요한 입력을 받는다. [공식 문서](https://developer.android.com/guide/topics/ui/dialogs?hl=ko)에서는 Dialog를 직접 인스턴스화 하지 않고 AlertDialog를 사용하도록 권장하고 있다.

<br />

AlertDialog는 빌더 패턴을 이용하면 간단하게 구현할 수 있다.

```kotlin
AlertDialog.Builder(this)
    .setTitle("제목")
    .setMessage("내용")
    .setPositiveButton("예") { _, _ ->
        Toast.makeText(this, "예", Toast.LENGTH_SHORT).show()
    }.setNegativeButton("아니오") { _, _ ->
        Toast.makeText(this, "아니오", Toast.LENGTH_SHORT).show()
    }.show()
```

![Alert dialog]({{ site.baseurl }}/images/android/custom_dialog/alert_dialog.png){: width="50%" height="50%"}{: .center-image}*Alert Dialog*

하지만 우리는 이런 칙칙한(?) 디자인의 다이얼로그를 사용하고 싶지 않다. 다이얼로그를 자유롭게 커스터마이징하려면 어떻게 해야 할까? 커스터마이징 하는 방법은 여러 가지가 있을 수 있지만, 기존의 AlertDialog와 비슷하게 빌더 패턴을 사용하여 구현해보자.

<br />

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        xmlns:tools="http://schemas.android.com/tools"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:layout_height="wrap_content">

    <TextView
            android:id="@+id/titleTextView"
            android:layout_width="0dp"
            android:layout_height="50dp"
            android:gravity="center"
            android:textSize="18sp"
            android:background="#FFC107"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toTopOf="@id/messageTextView"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent" />

    <TextView
            android:id="@+id/messageTextView"
            android:layout_width="0dp"
            android:layout_height="100dp"
            android:gravity="center"
            android:textSize="18sp"
            app:layout_constraintBottom_toTopOf="@id/guideline"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/titleTextView"/>

    <androidx.constraintlayout.widget.Guideline
            android:id="@+id/guideline"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:orientation="horizontal"/>

    <Button
            android:id="@+id/negativeButton"
            android:layout_width="0dp"
            android:layout_height="50dp"
            android:textSize="18sp"
            android:background="#9B9B9B"
            app:layout_constraintTop_toBottomOf="@id/guideline"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintRight_toLeftOf="@id/positiveButton"
            app:layout_constraintLeft_toLeftOf="parent" />

    <Button
            android:id="@+id/positiveButton"
            android:layout_width="0dp"
            android:layout_height="50dp"
            android:textSize="18sp"
            android:background="#FFC107"
            app:layout_constraintTop_toBottomOf="@id/guideline"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintLeft_toRightOf="@id/negativeButton" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

먼저 커스터마이징할 다이얼로그의 레이아웃을 작성한다.
필자는 제목, 내용, 버튼 두개를 넣었는데, 다이얼로그 구성은 필요한대로 넣으면 된다.

<br />

```kotlin
class CustomDialog(private val context: Context) {

    private val builder: AlertDialog.Builder by lazy {
        AlertDialog.Builder(context).setView(view)
    }

    private val view: View by lazy {
        View.inflate(context, R.layout.view_dialog, null)
    }

    private var dialog: AlertDialog? = null

    fun setTitle(@StringRes titleId: Int): CustomDialog {
        view.titleTextView.text = context.getText(titleId)
        return this
    }

    fun setTitle(title: CharSequence): CustomDialog {
        view.titleTextView.text = title
        return this
    }

    fun setMessage(@StringRes messageId: Int): CustomDialog {
        view.messageTextView.text = context.getText(messageId)
        return this
    }

    fun setMessage(message: CharSequence): CustomDialog {
        view.messageTextView.text = message
        return this
    }

    fun setPositiveButton(@StringRes textId: Int, listener: (view: View) -> (Unit)): CustomDialog {
        view.positiveButton.apply {
            text = context.getText(textId)
            setOnClickListener(listener)
        }
        return this
    }

    fun setPositiveButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.positiveButton.apply {
            this.text = text
            setOnClickListener(listener)
        }
        return this
    }

    fun setNegativeButton(@StringRes textId: Int, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            text = context.getText(textId)
            this.text = text
            setOnClickListener(listener)
        }
        return this
    }

    fun setNegativeButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            this.text = text
            setOnClickListener(listener)
        }
        return this
    }

    fun create() {
        dialog = builder.create()
    }

    fun show() {
        dialog = builder.create()
        dialog?.show()
    }

    fun dismiss() {
        dialog?.dismiss()
    }
}
```

CustomDialog 클래스를 작성한다. AlertDialog의 빌더를 그대로 사용한다. 핵심은 빌더 패턴을 이용하기 위해 다이얼로그를 설정하기 위한 메서드들이 CustomDialog를 반환하도록 것이다.

<br />

```kotlin
CustomDialog(this)
    .setTitle("제목")
    .setMessage("내용")
    .setPositiveButton("예") {
        Toast.makeText(this, "예", Toast.LENGTH_SHORT).show()
    }.setNegativeButton("아니오") {
        Toast.makeText(this, "아니오", Toast.LENGTH_SHORT).show()
    }.show()
```

만든 다이얼로그를 띄어보자. AlertDialog 코드와 매우 유사하다.

<br />

![Custom dialog]({{ site.baseurl }}/images/android/custom_dialog/custom_dialog.png){: width="50%" height="50%"}{: .center-image}*Custom Dialog*

커스터마이징한 다이얼로그가 실행된다!

<br />

이렇게 다이얼로그를 커스터마이징하여 사용하다보면 한 가지 불편한 점을 발견할 수 있는데, AlertDialog는 버튼을 누르면 다이얼로그가 종료되지만, 커스텀 다이얼로그는 버튼을 눌러도 다이얼로그가 종료되지 않는다. 그래서 매번 클릭 리스너에 다이얼로그를 종료하는 번거로운 절차를 해야 한다. OnTouchListener를 통해 이를 해결해보자.

<br />

```kotlin
class CustomDialog(private val context: Context) {

    private val builder: AlertDialog.Builder by lazy {
        AlertDialog.Builder(context).setView(view)
    }

    private val view: View by lazy {
        View.inflate(context, R.layout.view_dialog, null)
    }

    private var dialog: AlertDialog? = null

    // 터치 리스너 구현
    private val onTouchListener = View.OnTouchListener { _, motionEvent ->
        if (motionEvent.action == MotionEvent.ACTION_UP) {
            Handler().postDelayed({
                dismiss()
            }, 5)
        }
        false
    }

    fun setTitle(@StringRes titleId: Int): CustomDialog {
        view.titleTextView.text = context.getText(titleId)
        return this
    }

    fun setTitle(title: CharSequence): CustomDialog {
        view.titleTextView.text = title
        return this
    }

    fun setMessage(@StringRes messageId: Int): CustomDialog {
        view.messageTextView.text = context.getText(messageId)
        return this
    }

    fun setMessage(message: CharSequence): CustomDialog {
        view.messageTextView.text = message
        return this
    }

    fun setPositiveButton(@StringRes textId: Int, listener: (view: View) -> (Unit)): CustomDialog {
        view.positiveButton.apply {
            text = context.getText(textId)
            setOnClickListener(listener)
            // 터치 리스너 등록
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setPositiveButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.positiveButton.apply {
            this.text = text
            setOnClickListener(listener)
            // 터치 리스너 등록
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setNegativeButton(@StringRes textId: Int, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            text = context.getText(textId)
            this.text = text
            setOnClickListener(listener)
            // 터치 리스너 등록
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setNegativeButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            this.text = text
            setOnClickListener(listener)
            // 터치 리스너 등록
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun create() {
        dialog = builder.create()
    }

    fun show() {
        dialog = builder.create()
        dialog?.show()
    }

    fun dismiss() {
        dialog?.dismiss()
    }
}
```

OnTouchListener에서 ACTION_UP은 OnClickListener와 동일한데 이를 이용한 꼼수이다. 여기에 0.005초 후에 다이얼로그를 종료하는 코드를 넣으면 클릭 이벤트가 실행되고 다이얼로그가 종료된다.

<br />

커스텀 다이얼로그를 구현하기 위해 반드시 빌더 패턴을 이용할 필요는 없지만, 빌더 패턴을 이용할 경우 그때그때 필요한 기능만을 사용할 수 있다. AlertDialog와 같이 우선 안드로이드에서 제공하는 형태를 따르고, 후에 앱의 특성에 맞게 수정하는 것이 올바른 방향이라고 생각한다.

<br />