---
layout: default
title: Обработка взаимодействия
highlight: true
---

В этом уроке мы разберемся какие события ввода есть в Android и как их использовать.

## Обзор

В системе Android предусмотрено несколько способов перехвата событий взаимодействия пользователя с вашим приложением. Например, когда пользователь касается отображаемого объекта (кнопки), вызывается метод `onTouchEvent()` этого объекта.

Основные методы взаимодействия с `View`:

- `onClick()` из `View.OnClickListener`. Этот метод вызывается, когда пользователь касается элемента;
- `onLongClick()` из `View.OnLongClickListener`. Этот метод вызывается, когда пользователь касается элемента и удерживает его;
- `onFocusChange()` из  `View.OnFocusChangeListener`. Этот метод вызывается, когда пользователь делает активным или не активным текущий элемент;
- `onKey()` из `View.OnKeyListener`. Этот метод вызывается, когда пользователь переносит фокус на элемент и нажимает или отпускает клавишу на устройстве;
- `onTouch()` из `View.OnTouchListener`. Этот метод вызывается, когда пользователь выполняет действие, считающееся событием касания, например, нажимает, отпускает или выполняет любой жест на экране (в пределах границ элемента).
- `onCreateContextMenu()` из `View.OnCreateContextMenuListener`. Этот метод вызывается, когда создается контекстное меню.

 Чтобы определить один из этих методов и обрабатывать события, необходимо реализовать интерфейс в вашем проекте или определите его как анонимный класс. 

 Обратимся к документации. Обратите внимание, что вызов `onClick()` не содержит возвращаемого значения, но некоторые методы должны возвращать значение. Нужно возвращать значение или нет, зависит от события. Например:
`onLongClick()` — этот метод возвращает значение, указывающее, что вы обработали это событие: `true` - вы обработали событие и его следует остановить, `false` -  если вы не обработали его и/или событие должно продолжаться.


## onTouch

С простейшим onClick мы разобрались в предыдущем уроке. Давайте теперь посмотрим, что такое onTouch и с какими событиями он может работать. Для этого создадим новое `View` и установим ему `onTouchListener`:

```java
View touchView = findViewById(R.id.activity_main__touch);
touchView.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View v, MotionEvent event) {
        Log.e(TAG, event.toString());
        return false;
    }
});
```

Запустив приложение и коснувшись экрана, мы увидим одну строку:

```
2019-02-21 16:20:47.356 19804-19804/com.example E/polis-mail-ru: MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=543.0, y[0]=1072.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=227328288, downTime=227328288, deviceId=7, source=0x1002 }
```

В логах есть упоминание только о событии `ACTION_DOWN`, но если обратиться к документации, событий гораздо больше. Почему другие события не попали в логи? Дело в том, что мы вернули `false` в методе `onTouch`, следовательно, мы сами сообщили о том, что завершили обработку событий. Давайте теперь попробуем вернуть значение `true`:

```
2019-02-21 16:35:53.260 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_DOWN, actionButton=0, id[0]=0, x[0]=605.0, y[0]=549.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=228234191, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.429 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=605.0, y[0]=552.092, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=2, eventTime=228234358, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.445 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=605.0, y[0]=556.1218, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=2, eventTime=228234375, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.462 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=606.0, y[0]=559.3099, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=2, eventTime=228234391, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.479 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=606.0, y[0]=561.2624, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=2, eventTime=228234408, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.513 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=606.0, y[0]=562.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=228234431, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.529 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=606.0, y[0]=563.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=228234456, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.554 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_MOVE, actionButton=0, id[0]=0, x[0]=607.0, y[0]=564.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=228234482, downTime=228234191, deviceId=7, source=0x1002 }
2019-02-21 16:35:53.556 22701-22701/com.example E/polis-mail-ru: MotionEvent { action=ACTION_UP, actionButton=0, id[0]=0, x[0]=607.0, y[0]=564.0, toolType[0]=TOOL_TYPE_FINGER, buttonState=0, metaState=0, flags=0x0, edgeFlags=0x0, pointerCount=1, historySize=0, eventTime=228234489, downTime=228234191, deviceId=7, source=0x1002 }
```

Теперь в нашем логе появились новые события: `ACTION_MOVE` и `ACTION_UP`.