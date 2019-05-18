---
layout: default
title: Как быть если кругом Java? (Совместимость Java и Kotlin)
highlight: true
---

В этой теме мы рассмотрим совместимость Java и Kotlin.
Что делать если весь код на Java? Можно ли внедрять Kotlin, или необходимо все переписывать?
В основном на эти вопросы я дам ответы.

## Как быть если кругом Java?

### Interop Java в Kotlin (99.1%)

В первую очередь мы поговорим о том, есть ли какие либо проблемы при вызове `Java` кода из `Kotlin` кода.
(Спойлер!! Небольшие есть)

Первая проблема заключается именно в `null safety` у `Kotlin`.
В чем смысл этой проблемы, большинство типов из Java в Kotlin будут именоваться платформенными типами, 
то есть и `null` и `non-null` тип в Kotlin, а значит к ним применяются теже правила как и в Java, 
но стоит поставить аннотации над геттерами или полями `@Nullable` | `@NonNull`, 
`null safety` заработает как и прежде. 
Что вообще это все может значить? А то, что мы не можем быть уверены `null` ссылка или нет, даже если проставить аннотации, 
они не дадут этой безопасности, по причине того, что аннотация такого типа всего лишь дает предупреждение, 
и проставить ссылке `null` все равно возможно.
Что можно сделать в этом случае? по максимуму проставлять необходимые аннотации и следить, 
что бы правила, которые задают эти аннотации, соблюдались.

Следующая небольшая проблема, это то что ключевые слова из Kotlin мы должны экранировать, 
если например метод называется также как ключевое слово, пример:
```kotlin
animals.`as`(dog)
```

В Kotlin нет checked exception, поэтому код который может бросать checked Exception, в Kotlin нет необходимости обрабатывать.

<img src="img/java_checked_exception.png" width="800px"/>
<img src="img/kotlin_checked_exception.png" width="800px"/>

Я не указал, что это проблема, так как не считаю таковой эту ситуацию, но это лично мое мнение.

Также в Kotlin отличается работа с `Generics`. Например, `wildcards` конвертируются в `out` или `in` с платформенным типом.
А общий тип, без указания `generic`, допустим `List`, конвертируется в `List<*>!`, что эквивалентно `List<out Any?>!`

На этом основные проблемы заканчиваются, про остальное вы можете прочитать 
по ссылкам которые я указал в разделе `Что почитать`.

### Interop Kotlin в Java (87.048753%)

Теперь настало время поговорить о вызове `Kotlin` кода из `Java`. Тут все не так очевидно, как было с `Java` в `Kotlin`.
Основной момент такой, вам придется использовать приличное количество аннотаций.
Самые популярные аннотации, например `@JvmName`, `@JvmField`, `@JvmStatic`, `@Throws`, `@JvmOverloads`. 

Конечно это не весь список, но как показала практика это наиболее востребованные.
Сначала поговорим о `@JvmName`, эта аннотация дает вам возможность задать имя для метода, property или файла.
Например, из Java все kotlin файлы именуются с префиксом `Kt`, то есть был файл `extensions.kt`, в `Java` вызов будет таким `ExtensionsKt`, 
что бы заменить имя необходимо поставить эту аннотацию:
```kotlin
// file extensions.kt
@file: JvmName("Extensions")
package com.example

fun Context.showToast(text: String) {
}
```

Дальше посмотрим на `@JvmField`, эта аннотация указывает Java, 
что эта property является статической и имеет туже видимость что и класс в котором она находится.
```kotlin
class Formatter {
    companion object {
        @JvmField
        val DEFAULT_FORMAT: DecimalFormat = DecimalFormat("##0.0")
    }
}
```
В Java вызов будет таким `Formatter.DEFAULT_FORMAT`, если не использовать данную аннотацию, 
то вы столкнетесь с таким `Formatter.Companion.DEFAULT_FORMAT`. 
Это в случаях, когда у вас объекты (не String). Если используются примитивы или String, то достаточно пометить значение `const`.
```kotlin
class Formatter {
    companion object {
        const val DEFAULT_FORMAT: String = "##0.0"
    }
}
```

Теперь рассмотрим `@JvmStatic`, смысл точно такой же как и с property or const, но применять ее следует только к методам.
```kotlin
class Formatter {
    companion object {
        const val DEFAULT_FORMAT: String = "##0.0"

        @JvmStatic
        fun format() {
        }
    }
}
```

Аннотация `@Throws` нужна когда метод пробрасывает `checked exception`, что бы при вызове из Java, 
мы могли обернуть в `try catch` и не получить compile error такого типа.

<img src="img/throws.png" width="800px"/>

Вот как раз для решения этой проблемы достаточно проставить аннотацию:
```kotlin
@Throws(IOException::class)
fun test() {
    throw IOException()
}
```

И последняя аннотация которую мы рассмотрим это `@JvmOverloads`.
Ее необходимо применять, когда вы используете `Kotlin` классы в `Java` с конструкторами
которые используют дефолтные значения или методы у которых параметры также используют дефолтные значения.
Если не поставить эту аннотацию над методом или конструктором, то в Java, 
вам будет доступен только один метод или конструктор, со всеми параметрами.

Пример использования:
```kotlin
@JvmOverloads
fun showToast(
        context: Context,
        text: String = "",
        textRes: Int = -1,
        duration: Int = Toast.LENGTH_SHORT
)
```

Пример использования для Android View constructors:
```kotlin
class CustomView : View {
    constructor(context: Context)
            : super(context)
    constructor(context: Context, attrs: AttributeSet?)
            : super(context, attrs)
    constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int)
            : super(context, attrs, defStyleAttr)
    constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int, defStyleRes: Int)
            : super(context, attrs, defStyleAttr, defStyleRes)
}

// с помощью аннотации @JvmOverloads можно сократить запись.

class CustomView @JvmOverloads constructor(
        context: Context,
        attrs: AttributeSet? = null,
        defStyleAttr: Int = 0,
        defStyleRes: Int = 0
) : View(context, attrs, defStyleAttr, defStyleRes)
```

Для Android View необходимо быть осторожным, есть кое какие проблемы, 
почитать про это можете по ссылке в разделе `Что почитать`.

Про остальные аннотации можете почитать по тем ссылкам, что я указал в `Что почитать`.

В итоге ответы на первоначальные вопросы будут такими:
- Начинать писать новые фичи на Kotlin, если есть желание
- Переписывать с нуля не нужно, можно внедрять постепенно, но только учитывайте все вышеописанное.

## Что почитать

- https://kotlinlang.org/docs/reference/java-interop.html
- https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html
- https://developer.android.com/kotlin/interop
- https://medium.com/@elye.project/in-and-out-type-variant-of-kotlin-587e4fa2944c
- https://medium.com/@mmlodawski/https-medium-com-mmlodawski-do-not-always-trust-jvmoverloads-5251f1ad2cfe
- https://zsmb.co/jvmoverloads-for-android-views/

## Что посмотреть

- https://www.youtube.com/watch?v=1L0q5VKx_-s
