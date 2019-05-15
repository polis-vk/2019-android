---
layout: default
title: Спасение в DSL или нет?
highlight: true
---

В этой часте будет затронута такая тема как Kotlin DSL, а именно: 
Что это? Что позволяет сделать? Удобно ли использовать, то что получилось?

## Спасение в DSL или нет?

Сначала давайте разберемся, что это за зверь такой Kotlin DSL.
DSL - это domain-specific language (проблемно-ориентированный язык). 
Каждый такой язык решает свою узконаправленную задачу, например, запрос данных из БД, 
поиск совпадений в тексте или описание процесса сборки приложения. Примеры таких языков: SQL, RegExp, Gradle. 
Kotlin предоставляет конкретные инструменты для реализации собственного DSL.

Что бы лучше понять, можно взглянуть на готовые примеры, а именно такие как: Anko, Ktor, либо HTML Builder от JB, 
конечно это не весь список уже реализованных DSL.

Как я уже писал выше Kotlin DSL предоставляет конкретные инструменты для реализации собственного DSL:
- Переопределение операторов
- Лямбда за скобками
- Extension функции
- Infix функции
- Лямбда с обработчиком
- Контроль скоупа (@DslMarker)

Теперь давайте попробуем построить собственный DSL, сделаем простой для класса Paint в Android.

Сперва создадим функцию которая принимает в качестве параметра лямбу с обработчиком:
```kotlin
fun paint(configuration: Paint.() -> Unit): Paint {
    return Paint().apply {
        configuration()
    }
}
```

```Paint.() -> Unit``` это и есть лямбда с обработчиком, теперь в фигурных скобках мы можем обращаться к полям и методам Paint напрямую:
```kotlin
val paint = paint {
    alpha = 195
    color = Color.BLACK
}
```

По сути это уже и есть простой вид DSL, так как это новый способ инициализации класса Paint.
Теперь пойдем дальше и добавим возможность установить Shader для Paint. Работать будем в данном примере только с linearGradient
Для удобства работы с полями ```LinearGradient```, которые передаются ему в конструктор, мы создадим класс который по сути будет временным holder для этих полей:
```kotlin
class LinearGradientScope {
    var startX = 0f
    var endX = 90f
    var startY = 0f
    var endY = 90f
    var colors = intArrayOf(Color.GRAY, Color.BLACK)
    var tileMode = Shader.TileMode.MIRROR

    fun createGradient(): LinearGradient {
        return LinearGradient(startX, startY, endX, endY, colors, null, tileMode)
    }
}
```

Дальше необходимо создать функцию, которая будет нам создавать ```LinearGradientScope``` и сетить в него эти properties. Это основная функция которую мы будем использовать для установки ```Shader``` в ```Paint```.
```kotlin
fun linearGradient(configuration: LinearGradientScope.() -> Unit): Shader {
    val linearGradientScope = LinearGradientScope()
    configuration(linearGradientScope)
    return linearGradientScope.createGradient()
}
```

Теперь давайте установим ```LinearGradient``` в качестве ```Shader``` для класса ```Paint```.
```kotlin
val paint = paint {
    alpha = 195
    color = Color.BLACK
    shader = linearGradient {
        startX = 0f
        startY = 0f
        endX = 90f
        endY = 90f
    }
}
```

Вот мы и построили DSL для класса ```Paint```. Функционал можно увеличивать, допустим добавляя новые ```Shader's```. Также мы можем запретить вложенный ```linearGradient```, то есть вот такое:
```kotlin
class LinearGradientScope {
    var startX = 0f
    var endX = 90f
    var startY = 0f
    var endY = 90f
    var colors = intArrayOf(Color.GRAY, Color.BLACK)
    var tileMode = Shader.TileMode.MIRROR

    fun createGradient(): LinearGradient {
        return LinearGradient(startX, startY, endX, endY, colors, null, tileMode)
    }
    
    @Deprecated("Вложенный вызов запрещен", level = DeprecationLevel.ERROR)
    fun linearGradient(configuration: LinearGradientScope.() -> Unit): Shader {
        val linearGradientScope = LinearGradientScope()
        configuration(linearGradientScope)
        return linearGradientScope.linearGradient()
    }
}
```

Это своеобразный хак, но рабочий, такое решение можно заменить с помощью аннотации ```@DslMarker```, она была добавлена как раз для подобных ситуаций.
Более подробно про Kotlin DSL вы можете почитать по ссылкам, которые приведены в разделе ```Что почитать```. Также там есть код других Kotlin DSL, который вы можете посмотреть.

Если отвечать на главный вопрос этой части лекции, то каждый решает для себя, в каких то моментах DSL может лишь навредить читаемости кода, 
поэтому тут нужно уметь сохранять баланс и не уходить слишком далеко вправо :)
Это полезный функционал языка Kotlin и с ним необходимо уметь работать.

## Что почитать
- https://github.com/Kotlin/anko
- https://ktor.io/
- https://github.com/InsertKoinIO/koin
- https://github.com/arunkumar9t2/transition-x
- https://kotlinlang.org/docs/reference/type-safe-builders.html
- https://habr.com/ru/company/haulmont/blog/341402/


