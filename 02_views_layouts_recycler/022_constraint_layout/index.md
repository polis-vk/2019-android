---
layout: default
title: ConstraintLayout
highlight: true
---

В этом уроке мы научимся с самым мощным лейаутом - `ConstraintLayout`.

## Обзор

`Constraints` — это линии разметки, на основе которых располагаются `View` внутри `ConstraintLayout`. `Constraints` могут быть привязаны к сторонам родителя или к сторонам других `View`. `ConstraintLayout` это `RelativeLayout` на стероидах. `Constraints` можно разделить на вертикальные и горизонтальные.

Горизонтальные `Constraints`:

- правая сторона (right);
- левая сторона (left);
- начало элемента (start);
- конец элемента (end).

Вертикальные `Constraints`:

- верхняя сторона (top);
- нижняя сторона (bottom);
- базовая линия (baseline).

`Baseline` — это линия выравнивания контента. Например, для `TextView` - это линия строки, на которой пишется текст. Если у `View` выставлен `Baseline Constraint`, то базовая линия этого `View` будет находиться на уровне базовой линии `View`, к которой привязан `Сonstraint`.

Общий формат привязки `Constraint` выглядит следующим образом:

```xml
  app:layout_constraint{X}_to{Y}Of="{Z}"
```

где:

- `X` — `Constraint` привязываемой `View`;
- `Y` — сторона `View`, к которой привязываются;
- `Z` — `id` `View`, к которой привязываются, или `parent`.

 Для того, что бы начать использовать `ConstraintLayout`, необходимо добавить зависимость в `build.gradle` файл:

```groovy
dependencies {
...
    compile 'com.android.support.constraint:constraint-layout:1.1.3'
...
}
```

## Привязка границ, настройка ширины и высоты

**Основные правила привязки сторон**:

- привязывать между собой можно только `Start` и `End`, `Left` и `Right`, `Top` и `Bottom`. **Нельзя** привязать `Left` к `Start` или `Baseline` к `Top`!
- **не** привязывайте view с внешней стороны родителя, например, `layout_constraintRight_toLeftOf="parent"`!
- при привязке `Start` или `End` игнорируются привязки `Left`и `Right`;
- `Baseline` можно привязать только к `Baseline`;
- при привязке `Baseline` игнорируются привязки `Top` и `Bottom`.



**Размеры View**

Чтобы задать размеры `View`, используются обязательные атрибуты `layout_width` и `layout_height`, и необязательные атрибуты `layout_constraintWidth_default` и `layout_constraintHeight_default`. Значение атрибутов `layout_constraintWidth_default` и `layout_constraintHeight_default` по умолчанию равно `spread`.

Рассмотрим на примере ширины, каким образом нам задавать значения:

1. Фиксированный размер `View`. `layout_width="100dp"`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="100dp"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Very very long text text text text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_fixed_width.png" width="400px"/>

2. Разрешаем `View` самой рассчитать свой размер. `layout_width="wrap_content"`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Very very long text text text text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_wrap_width.png" width="400px"/>

3. Разрешаем View заполнить всю доступную ширину. `layout_width="0dp",  layout_constraintWidth_default="spread"`. По-умолчанию `layout_constraintWidth_default` уже имеет значение `spread`, его можно не писать.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Very very long text text text text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintWidth_default="spread" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_long_spread.png" width="400px"/>

4. Разрешаем View самой рассчитать свой размер, но не больше чем доступная ширина. `layout_width="0dp", layout_constraintWidth_default="wrap"`.

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Very very long text text text text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintWidth_default="wrap" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_long_wrap.png" width="400px"/>

Результат получился таким же, как и в предыдущем случае, это связано с тем, что у нас достаточно большой текст. Давайте попробуем уменьшить длинную текста и посмотрим что получится:

`layout_width="0dp",  layout_constraintWidth_default="spread"` для короткого текста:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintWidth_default="spread" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_short_spread.png" width="400px"/>

`layout_width="0dp",  layout_constraintWidth_default="wrap"` для короткого текста:

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="bottom">

    <View
        android:id="@+id/left"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:layout_width="0dp"
        android:layout_height="300dp"
        android:background="#FF0000"
        android:text="Text"
        android:textSize="30sp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toStartOf="@id/right"
        app:layout_constraintStart_toEndOf="@id/left"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintWidth_default="wrap" />

    <View
        android:id="@+id/right"
        android:layout_width="100dp"
        android:layout_height="0dp"
        android:background="#FFFF00"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintTop_toBottomOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</android.support.constraint.ConstraintLayout>
```

<img src="img/constraint_short_wrap.png" width="400px"/>


Аналогичная работают атрибуты высоты `View`. При использовании значения `wrap_content` будьте внимательны и не забывайте, что иногда ваша `View` может выйти за границы `Constraints`

**Нельзя** использовать значения `match_parent` или `fill_parent`!

