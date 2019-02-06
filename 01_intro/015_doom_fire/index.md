---
layout: default
title: Приложение огонь!
---

В этом уроке мы немного поработаем с основами графики, и сделаем просто огненное приложение, которое будет выглядеть приблизительно так:

<img src="img/fire.gif" width="346px"/>

Это приложение воспроизводит эффект огня из игры Doom на Playstation, идея реализации подробно описана в [статье Фабьена Санглара](http://fabiensanglard.net/doom_fire_psx/) (автора книги Game Engine Black Book: Doom). На Хабре есть [перевод этой статьи на русский язык](https://habr.com/ru/post/435122/).

## Рисование на экране

Прежде чем мы начнем писать огненный код, разберемся, как работать с графикой на Android.

Как мы уже знаем, всё, что отображается на экране Android устройства -- это View. В распоряжении разработчика есть набор  View из Android SDK для отображения стандартных компонентов интефейса: текста, кнопок, картинок... Если нужно нарисовать что-то нестандартное, то нужно это делать так же при помощи View, но этот View придется написать самим. За отрисовку на экране содержимого View отвечает метод `View.onDraw(Canvas canvas)`, который надо переопределить и добавить в него код, который нарисует то, что надо. Общий алгоритм действий для рисования чего-то при помощи View, следующий:

- Создать свой класс, наследующий от View
- Переопределить в нем метод `View.onDraw(Canvas canvas)`
- Написать код, который рисует то, что надо
- Добавить созданный кастомный View в верстку, как любой другой View.

*Примечание: такие нестандартные View, написанные руками разработчиков, которые рисуют что-то необычное, и, вообще, ведут себя не так, как стандартные View из Android SDK, часто называются Custom View или кстомные View. Умение делать кастомные View часто упоминается в вакансиях и резюме Android разработчиков*

## Canvas и Paint

Чтобы рисовать, нам понадобятся классы `android.graphics.Canvas` и `android.graphics.Paint`.

Первый (Canvas) содержит методы вида `drawSomething` для рисования графических объектов и отвечает за отрисовку финального изображения. В метод `onDraw(Canvas canvas)` объект `Canvas` приходит в качестве аргумента -- этот объект связан с отображением `View` на экране, и все вызовы на нём будут перобразованы в низкоуровневые команды графического процессора (Open GL), которые будут выполнены при формирования кадра в графической памяти перед отрисовкой на экране. Подробно о том, как устроена графическая подсистема Android, можно прочитать в статье [Graphics architecture](https://source.android.com/devices/graphics/architecture), но пока можно считать, что, вызывая методы `Canvas.drawSomething` мы просто рисуем внутри нашего View на экране.

Класс `Paint` отвечает за то, *как* мы рисуем -- каким цветом, стилем, какой толщины линиями и т.п. Мы сами создаем объект `Paint` в коде и используем его при необходимости.

## Примеры рисования на Canvas

Для того, чтобы попрактиковаться с рисованием на Canvas, создайте новый проект Android приложения, а в нём класс `DemoDrawingView`, который наследует от `View`:
```
public class DemoDrawingView extends View {

    public DemoDrawingView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }
}
```

Конструктор с сигнатурой `<init>(Context, AttributeSet)` обязательно нужен для того, чтобы этот класс можно было использовать в верстке.

Добавьте в проект одну основную активность, и используйте в ней следующую верстку (обратите внимание на имя пакета у DemoDrawingView):
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent" 
    android:layout_height="match_parent">
    
    <ваше-имя-Java-пакета.DemoDrawingView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#000"/>

</FrameLayout>
```

Здесь мы используем атрибут `background` для задания цвета фона нашего View. Мы хотим, чтобы он был черным (на черном как-то удобнее рисовать), поэтому его значение равно `#000` -- это сокращение от шестнадцатиричного `ff000000` (черный цвет в ARGB цветовой модели).

В классе `DemoDrawingView` переопределите метод `onDraw(Canvas)`:
```
    @Override
    protected void onDraw(Canvas canvas) {
        int width = getWidth();
        int height = getHeight();

        // Will draw in rect (0,0) - (width, height)
        // Put your custom drawing code here
    }
```

Сейчас в методе `onDraw` нет никакого кода, который что-то рисует, поэтому, если запустить сейчас приложение, то мы увидим просто черный экран -- это пустой `DemoDrawingView` на черном фоне, занимающий весь экран.

Рисование происходит в пространстве координат View и ограничено прямоугольной областью, которую занимает View. Левый верхний угол имеет координаты `(0.0)`, а правый нижний -- `(width, height)`, где `width` и `height` это ширина и высота View, которые можно получить при помощи методов `getWidth()` и `getHeight()`.

Для вызова некоторых методов отрисовки нам понадобится объект `Paint` для того, чтобы указывать цвет. Мы создадим один инстанс `Paint` и сохраним в поле класса `DemoDrawingView`, чтобы переиспользовать его в дальнейшем:

```
	private final Paint paint = new Paint();
```

Ниже будут приведены примеры кода, который надо встравить в метод `onDraw()` после комментария *Put your custom drawing code here*.

### Заливка цветом

```
	canvas.drawColor(Color.BLUE);
```

<img src="img/0801_fill_color.png"/>

### Прямоугольники

```
    paint.setColor(Color.GREEN);
    
	final int size = 300;
    for (int x = 0; x < width; x += size) {
        for (int y = 0; y < height; y += size) {
            paint.setColor(0x00ffffff & (1257823419 * x + 2118746214 * y) | 0xff000000);
            canvas.drawRect(x, y, x + size, y + size, paint);
        }
    }
```

<img src="img/0802_rectangles.png"/>

### Круги

```
    int centerX = width / 2;
    int centerY = height / 2;
    int padding = 50;
    int minSide = Math.min(width, height);
    int contentSize = minSide - 2 * padding;
    int overlap = 300;
    int radius = (contentSize + overlap) / 4;
    int dist = 2 * radius - overlap;

    float x1 = centerX - dist / 2;
    float x2 = centerX + dist / 2;
    float x3 = centerX;

    float d = dist / 2 / (float) Math.sqrt(3f);
    float y1 = centerY + d;
    float y2 = y1;
    float y3 = centerY - 2 * d;

    paint.setColor(Color.YELLOW & 0x7fffffff);
    canvas.drawCircle(x1, y1, radius, paint);
    paint.setColor(Color.MAGENTA & 0x7fffffff);
    canvas.drawCircle(x2, y2, radius, paint);
    paint.setColor(Color.BLUE & 0x7fffffff);
    canvas.drawCircle(x3, y3, radius, paint);
```

<img src="img/0803_circles.png"/>