---
layout: default
title: Powerful Kotlin для Android
highlight: true
---

В этой теме мы рассмотрим полезные возможности Kotlin под Android.

## Powerful Kotlin для Android

### Kotlin Android Extensions

Эта тема никак не связанна с extensions functions в Kotlin, она про другое, 
а именно про view binding и @Parcelize. Это те возможности, которые были разработаны `JB (JetBrains)` специально под Android.
Необходимо учитывать, что часть функционала находится еще в experimental режиме.

Начнем с view binding.
Самые популярные строки кода в Android это `findViewById` или просто `fib`.
Нам приходится писать их слишком часто и в большом количестве, если допустим у нас сложный UI.
Для борьбы с уменьшением количества этих строчек кода были придуманы различные решения, одно из них, 
это `ButterKnife` от `JakeWharton`.
Я не буду подробно здесь описывать, что делает эта библиотека, но смысл в том, 
что строки `fib` заменяются на аннотации поверх полей, что то такое:
```java
@BindView(R.id.title_text_view) 
TextView name;
```
Она конечно делает не только это, но смысл думаю понятен.
Так вот, в `Kotlin`, можно подключить `kotlin-android-extensions` plugin в Gradle. И забыть про `fib`.
Пример:
```kotlin
//R.id.title_text_view id TextView
title_text_view.text = "Hello"
```

Конечно, у этого способа есть свои подводные камни, но в общем случае, это отличное решение.
В Activity вы можете использовать на стадии метода `onCreate`.
В Fragment правильным решением будет использовать на стадии `onViewCreated`
Также есть поддержка и других классов, используя `LayoutContainer`.
Пример с `ViewHolder`:
```kotlin
class MyViewHolder(override val containerView: View) : ViewHolder(containerView), LayoutContainer {
    fun setup(title: String) {
        itemTitle.text = "Hello World!"
    }
}
```

Как же все это работает? На самом деле очень просто. За вас создается специальный кеш, 
куда при первом вызове, через `fib` складываются необходимые View, а после уже достаются оттуда, что намного быстрее, чем каждый раз запрашивать через `fib`.
Этот кеш вы можете изменять, по умолчанию там создается `HashMap`, но есть еще один вариант, а именно `SparseArray`.
```kotlin
@ContainerOptions(cache = CacheImplementation.SPARSE_ARRAY)
class MyActivity : Activity()
```

Также есть возможно поменять кеш глобально в `build.gradle`, что бы не проставлять аннотацию над каждым классом.
```kotlin
androidExtensions {
    defaultCacheImplementation = "HASH_MAP" // also SPARSE_ARRAY, NONE
}
```

Дальше мы посмотрим на такую аннотацию как `@Parcelize`.
По названию, наверное уже понятно, что она будет делать. Если мы ее укажем над классом и реализуем интерфейс `Parcelable`, 
то все необходимое будет сгенерировано за нас.
Необходимо учитывать, что аннотация работает только когда параметры конструктора являются `properties` и объявлены в `primary constructor`.
```kotlin
@Parcelize
class Address(
    val shortStreet: String,
    val longStreet: String,
    val latitude: Double,
    val longitude: Double
): Parcelable
```

### Android KTX, Kotlin friendly SDK, D8/R8

Android KTX это библиотека которая содержит в себе большое количество различных extensions для Android классов. 
Создана компанией Google.
Вот несколько интересных примеров:
```kotlin
// extensions для класса Uri
val uri = "address_to_file".toUri()
uri.toFile()

//до extension для Bundle
val bundle = Bundle().apply {
    putBoolean("KEY", false)
    putFloat("KEY_FLOAT", 0.5f)
    putIntArray("KEY_ARRAY", intArrayOf(55, 21))
}

//после extension для Bundle, можно заметить, что наполнение похоже на map
val bundle = bundleOf("KEY" to false, "KEY_FLOAT" to 0.5f, "KEY_INT_ARRAY" to intArrayOf(55, 21))

//extension для Drawable, преобразование в Bitmap
val bitmap = drawable.toBitmap()

//extension в стиле DSL, для Span
val string = buildSpannedString {
	append("text")
	bold {
		append("bold text")
		italic { append("bold and italic text") }
	}
	inSpans(RelativeSizeSpan(2f), QuoteSpan()) {
		append("double sized quote text")
	}
}
```
Остальные примеры вы можете посмотреть лично, подключив к проекту ktx.

Хотелось бы упомянуть об основной проблеме взаимодействия с Android SDK из Kotlin, это именно null safety, 
но Google в этом плане делает улучшения, а именно старается покрыть все необходимое аннотациями, об этом можно посмотреть здесь:
https://android-developers.googleblog.com/2018/08/android-pie-sdk-is-now-more-kotlin.html
Начало положено с Android Pie, и они продолжают повышать покрытие в Android Q.

//TODO описать про D8/R8 оптимизиации


## Что почитать

- https://proandroiddev.com/kotlin-android-extensions-using-view-binding-the-right-way-707cd0c9e648
- https://kotlinlang.org/docs/tutorials/android-plugin.html
- https://kotlinexpertise.com/android-ktx-kotlin/

