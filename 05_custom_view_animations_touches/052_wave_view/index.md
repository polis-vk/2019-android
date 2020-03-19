---
layout: default
title: Wave View
highlight: true
---

В этом уроке мы рассмотрим как создать свой `View` на примере аудиосообщения.

## Wave View

Предположим, что во время разработки приложения нам понадобилось отобразить аудиосообщение. Как же отобразить кнопки и прочие элементы мы уже знаем, но как отрисовать столбики громкости?

<img src="img/audiomessage.png" width="600"/>

Для этого нам понадобиться создать собственное `View`. Для этого унаследуемся от базового класса `View` и определим конструкторы:

```java
public class WaveView extends View {

    public WaveView(Context context) {
        this(context, null);
    }

    public WaveView(Context context, @Nullable AttributeSet attrs) {
        super(context, attrs);
    }
}
```    

и добавим полученный `WaveView` в разметку активити:

```xml
<<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ru.ok.technopolis.customview.WaveView
        android:layout_width="200dp"
        android:layout_height="100dp"
        android:background="#ddf3c2"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

Запустив проект мы ничего не увидим кроме зеленого прямоугольника (мы задали зеленый фон для `View`). Размеры прямоугольника равны значениям выставленным в `layout_width` и `layout_height`.

<img src="img/empty_wave.png" width="400px"/>

Для начала подготовим входные данные. Пускай у нас есть некий источник массива с данными о громкости аудиосообщения `WaveRepository`. Метод `getWaveData` возвращает массив, где каждый элемент описывает громкость аудисоообщения в заданный промежуток времени.

```java
public class WaveRepository {

    public static final int MAX_VOLUME = 100;
    private static final int WAVE_LENGTH = 200;

    public static int[] getWaveData() {
        int[] data = new int[WAVE_LENGTH];
        Random r = new Random();
        for (int i = 0; i < data.length; i++) {
            int value = r.nextInt(MAX_VOLUME + 1);
            data[i] = value;
        }
        return data;
    }

}
```

## onMeasure

Для того, что бы Android смог отрисовать наш `WaveView`, его нужно измерить. Определим толщину линии каждого столбика громкости, для этого в файл `dimens.xml` внесем соответствующее значение. Важно помнить, что задаем размеры в`dp` а не в `px`!

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <dimen name="wave_view_item_width">2dp</dimen>
</resources>
```

и считаем это значение в конструкторе `View`:

```java
itemWidth = (int) getResources().getDimension(R.dimen.wave_view_item_width);
```

 Приступаем непосредственно к расчету размеров `View`, переопределяем метод

```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec)
```

Первым шагом узнаем какие требования предъявляет родитель к нашему `View`:

```java
int measuredWidth = MeasureSpec.getSize(widthMeasureSpec);
int widthMode = MeasureSpec.getMode(widthMeasureSpec);

int measuredHeight = MeasureSpec.getSize(heightMeasureSpec);
int heightMode = MeasureSpec.getMode(heightMeasureSpec);
```

Далее нам необходимо посчитать желаемый размер `WaveView`. Будем рисовать каждый столбик размером с `itemWidth` и между столбиками сделаем интервал в `itemWidth`:

```java
int width = originalData.length * itemWidth * 2 - itemWidth;
```

Из предыдущего урока мы знаем, что родитель может запросить размер меньший чем мы планирум и мы не имеем права отказать. В этом случае необходимо интерполировать входные данные с громкостью. Для интерполяции будем использовать класс с простейшей  интерполяцией [LinearInterpolation](https://github.com/ajbanks/arff-builder/blob/3fc8f6860a37d33cd93081a9c8c0025fd70872f5/src/stretching/LinearInterpolation.java).
Высоту `View` мы всегда хотим использовать ту, которую предоставит родитель.

Код для расчета ширины:
```java
int width = originalData.length * itemWidth * 2 - itemWidth;
switch (widthMode) {
    case View.MeasureSpec.EXACTLY:
        if (width > measuredWidth) {
            int itemCount = (measuredWidth + itemWidth) / (itemWidth * 2);
            measuredData = LinearInterpolation.interpolateArray(originalData, itemCount);
        } else {
            measuredData = originalData;
        }
        width = measuredWidth;
        break;
    case View.MeasureSpec.AT_MOST:
        if (width > measuredWidth) {
            int itemCount = (measuredWidth + itemWidth) / (itemWidth * 2);
            measuredData = LinearInterpolation.interpolateArray(originalData, itemCount);
            width = measuredWidth;
        } else {
            measuredData = originalData;
        }
        break;
    case View.MeasureSpec.UNSPECIFIED:
        width = measuredWidth;
        measuredData = originalData;
        break;
}
int height = MeasureSpec.getSize(heightMeasureSpec);
```        

И в конце не забываем установить рассчитанные размеры

```java
setMeasuredDimension(width, height);
```

Запустив написанный код, мы по прежнему увидим зеленый прямоугольник по центру экрана. 

## onDraw

В методе onDraw нужно выполнить отрисовку нашего элемента. То, на чем мы рисуем - это `Canvas`, а то, чем рисуем - это `Paint`. `Canvas` является аргументом метода `onDraw`, а `Paint` необходимо создать и настроить. Помним о том, что лучше не создавать никаких объектов во время отрисовки, и создаем `Paint` в конструкторе:

```java
linePaint = new Paint();
linePaint.setStyle(Paint.Style.STROKE);
linePaint.setColor(Color.BLACK);
linePaint.setStrokeWidth(itemWidth);
```

Рисовать будем используя класс `Path`, который позволяет задавать произвольную форму графического объекта. `Path` также нужно создать в конструкторе:

```java
Path wavePath = new Path();
```

И напишем сам код отрисовки:

```java
@Override
protected void onDraw(Canvas canvas) {
    if (measuredData == null) {
        return;
    }
    wavePath.reset();
    int currentX = itemWidth;
    for (int data : measuredData) {
        float height = ((float) data / WaveRepository.MAX_VOLUME) * getMeasuredHeight();
        wavePath.moveTo(currentX, 0);
        wavePath.lineTo(currentX, height);
        currentX += itemWidth * 2;
    }
    canvas.drawPath(wavePath, linePaint);
}
```

Запустив приложение мы увидим что-то похожее на желаемые столбики громкости.

<img src="img/wave_simple.png" width="400px"/>

Столбцы идут сверху вниз. Это связано с тем, что в интерфейсе Android точкой отчета является верхний левый угол. Сдвинем полоски на центр:

```java
@Override
protected void onDraw(Canvas canvas) {
    if (measuredData == null) {
        return;
    }
    wavePath.reset();
    int measuredHeight = getMeasuredHeight();
    int currentX = itemWidth;
    for (int data : measuredData) {
        float height = ((float) data / WaveRepository.MAX_VOLUME) * measuredHeight;
        float startY = (float) measuredHeight / 2f - height / 2f;
        float endY = startY + height;
        wavePath.moveTo(currentX, startY);
        wavePath.lineTo(currentX, endY);
        currentX += itemWidth * 2;
    }
    canvas.drawPath(wavePath, linePaint);
```

<img src="img/wave_center.png" width="400px"/>

Посмотрим как будет выглядеть `WaveView` при разных значения ширины и высоты:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ru.ok.technopolis.customview.WaveView
        android:id="@+id/activity_main__wv_1"
        android:layout_width="match_parent"
        android:layout_height="100dp"
        android:background="#ddf3c2"
        app:layout_constraintBottom_toTopOf="@+id/activity_main__wv_2"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <ru.ok.technopolis.customview.WaveView
        android:id="@+id/activity_main__wv_2"
        android:layout_width="300dp"
        android:layout_height="100dp"
        android:background="#ddf3c2"
        app:layout_constraintBottom_toTopOf="@+id/activity_main__wv_3"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/activity_main__wv_1" />

    <ru.ok.technopolis.customview.WaveView
        android:id="@+id/activity_main__wv_3"
        android:layout_width="200dp"
        android:layout_height="50dp"
        android:background="#ddf3c2"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/activity_main__wv_2" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/wave_size.png" width="400px"/>

## Доработки

Для того что бы можно было удобно пользоваться созданной `View` нужно доработать следующие моменты:

1) наличие падингов;
2) возможность изменения толщины линии;
3) возможность изменения цвета линии.

<img src="img/wave_final.png" width="400px"/>

Код примера можно посмотреть [тут](https://github.com/polis-mail-ru/polis-mail-ru-customview-sample)

## Что почитать

[Предыдущий урок](../051_custom_view/)
