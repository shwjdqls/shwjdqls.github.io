---
layout: post
title: "[안드로이드] 커스텀 다이얼로그(Custom dialog) 만들기 - AlertDialog"
category: Android
tags: [Android, Dialog, Custom, AlertDialog]
comments: true
---

다이얼로그(Dialog)는 사용자에게 추가 정보의 입력이나 내용 확인을 위한 작은 창으로, 사용자가 계속 진행하기 위해 필요한 조치를 한다. [공식 문서](https://developer.android.com/guide/topics/ui/dialogs?hl=ko)에서는 Dialog를 직접 인스턴스화 하지 않고 AlertDialog를 사용하도록 권장하고 있다.

AlertDialog는 빌더 패턴을 사용하여 다음과 같이 구현한다.

(AlertDialog 사용 코드)

그러면 이렇게 나온다.

(AiertDialog 이미지)

그런데 다이얼로그를 커스텀해서 디자인을 자유롭게 적용하고 싶다.

다이얼로그를 커스터마이징 해보자

(CustomDialog 구현 코드)

기존의 다이얼로그와 비슷하게 사용할 수 있도록 위와 같이 구현한다.

(CustomDialog.xml)

예시로 뷰는 이렇게 만들어 보았다.

(CustomDialog 사용 코드)

사용할 때는 이렇게 사용한다.

(CustomDialog 이미지)




```kotlin
package com.jacob.customdialog

import android.os.Bundle
import android.util.Log
import android.view.View
import android.widget.Toast
import androidx.appcompat.app.AlertDialog
import androidx.appcompat.app.AppCompatActivity
import kotlinx.android.synthetic.main.activity_main.*

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        alertDialogButton.setOnClickListener {
            AlertDialog.Builder(this)
                .setTitle("제목")
                .setMessage("내용")
                .setPositiveButton("예") { _, _ ->
                    Toast.makeText(this, "예", Toast.LENGTH_SHORT).show()
                }.setNegativeButton("아니오") { _, _ ->
                    Toast.makeText(this, "아니오", Toast.LENGTH_SHORT).show()
                }.show()
        }

        customDialogButton.setOnClickListener {
            CustomDialog(this)
                .setTitle("제목")
                .setMessage("내용")
                .setPositiveButton("예") {
                    Toast.makeText(this, "예", Toast.LENGTH_SHORT).show()
                }.setNegativeButton("아니오") {
                    Toast.makeText(this, "아니오", Toast.LENGTH_SHORT).show()
                }.show()
        }
    }
}
```

```kotlin
package com.jacob.customdialog

import android.content.Context
import android.os.Handler
import android.view.MotionEvent
import android.view.View
import android.view.WindowManager
import androidx.annotation.StringRes
import androidx.appcompat.app.AlertDialog
import kotlinx.android.synthetic.main.view_dialog.view.*

class CustomDialog(private val context: Context) {

    private val builder: AlertDialog.Builder by lazy {
        AlertDialog.Builder(context).setView(view)
    }
    private val view: View by lazy {
        View.inflate(context, R.layout.view_dialog, null)
    }

    private var dialog: AlertDialog? = null

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
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setPositiveButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.positiveButton.apply {
            this.text = text
            setOnClickListener(listener)
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setNegativeButton(@StringRes textId: Int, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            text = context.getText(textId)
            this.text = text
            setOnClickListener(listener)
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun setNegativeButton(text: CharSequence, listener: (view: View) -> (Unit)): CustomDialog {
        view.negativeButton.apply {
            this.text = text
            setOnClickListener(listener)
            setOnTouchListener(onTouchListener)
        }
        return this
    }

    fun create() {
        dialog = builder.create()
    }

    fun show() {
        dialog = builder.create()
        dialog?.window?.let {
            it.setFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE, WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE)
            it.addFlags(WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM)
            it.decorView.systemUiVisibility = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION or View.SYSTEM_UI_FLAG_FULLSCREEN or View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY
            dialog?.show()
            it.clearFlags(WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE)
        } ?: dialog?.show()
    }

    fun dismiss() {
        dialog?.dismiss()
    }
}

```


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

![메인 화면]({{ site.baseurl }}/images/android/custom_dialog/main.png){: width="50%" height="50%"}{: .center-image}*메인 화면*

![Alert dialog]({{ site.baseurl }}/images/android/custom_dialog/alert_dialog.png){: width="50%" height="50%"}{: .center-image}*메인 화면*

![Click Ok in alert dialog]({{ site.baseurl }}/images/android/custom_dialog/alert_dialog_ok.png){: width="50%" height="50%"}{: .center-image}*메인 화면*

![Custom dialog]({{ site.baseurl }}/images/android/custom_dialog/custom_dialog.png){: width="50%" height="50%"}{: .center-image}*메인 화면*

![Click ok in custom dialog]({{ site.baseurl }}/images/android/custom_dialog/custom_dialog_ok.png){: width="50%" height="50%"}{: .center-image}*메인 화면*