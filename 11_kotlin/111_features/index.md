---
layout: default
title: Зачем нам это все? (Основные features языка)
highlight: true
---

В первую очередь мы с вами рассмотрим основные возможности `Kotlin`, 
которые помогут вам во время разработки.

## Зачем нам это все?

### Null safety
Первое о чем мы с вами поговорим это о `Null safety`.
Все мы страдали от того, что в каких то ситуациях, иногда не зависящих 
от нас мы получаем такое вредное исключение как `NullPointerException` (NPE).
Так вот Kotlin помогает вам избегать таких нелепых ситуаций, защищает вас от `NPE`.
Для этого в нем есть 2 типа, первый это `Nullable` тип, который помечается так `?` (`String?`), 
а второй `Non-Null` тип, записывается как обычный тип в `Java` (`String`).
Что это нам дает? А дает это нам увереность в том, что если тип объявлен как `Non-Null`, 
то `NPE` нам тут не светит и можем спать спокойно.

Давайте посмотрим на пример:

```kotlin
val nullableString: String? = null
val nonNullString: String = ""
```

Если попробовать присвоить null к `Non-Null` типу, то компилятор подсветит вам это как ошибку.

<img src="img/nullsafety/non_null_type_with_null.png" width="1200px"/>

Дальше больше, как теперь понять, что мы защищены от `NPE`?
Все просто, тот же компилятор поможет нам в этом. Если мы вызываем на какой либо nullable переменной функцию этого класса, 
компилятор нам скажет, что необходимо поставить оператор `?` или `!!` что бы все заработало.

<img src="img/nullsafety/call_function.png" width="1200px"/>

Теперь перейдем сооветственно к операторам `?` и `!!`.
Оператор безопасного вызова `?` убережет вас в случае если ссылка у которой пытаются вызвать функцию равна null, 
также стоит упомянуть тут, что благодаря elvis оператору `?:` вы можете забыть о громоздких `if-else`, таких как:

```kotlin
val text: String? = null

// без elvis оператора
if (text == null) {
    return
}

// с elvis оператором
text?.length ?: return
```
Elvis оператор очень полезен во многих ситуациях, допустим с помощью него вы можете делать ранний выход как выше описано,
либо делать non-null shadow на ссылки и многое другое.
Если вам не хочется постоянно ставить оператор `?`, то вы можете написать простую extension функцию с помощью функции `let`, 
например:

```kotlin
fun <T : Any, R> T?.notNull(block: (T) -> R): R? {
    return this?.let(block)
}

val text: String? = null

text.notNull {
}
```

Но пример выше не сильно отличается от того же:

```kotlin
if (text != null) {
    // что-то
}
```
Поэтому на любителя.

Оператор `!!` является небезопасным вызывом, так как если ссылка равна null, 
то мы упадем с ошибкой `KNPE` (KotlinNullPointerException). 

<img src="img/nullsafety/knpe.png" width="1200px"/>

При написании кода, необходимо стараться не пользоваться часто этим оператором, лучше отдать предпочтение оператору `?`, 
но думать никто не отменял, поэтому смотрите по ситуации.

Есть еще один exception, который иногда портит жизнь, хотя конечно он очень полезен.
Речь идет о ClassCastException, и с ним нам поможет оператор безопасного приведения типов `as?`

```kotlin
anyObject as? Number
```
Вам вернется null, в случае если привести тип не удалось. Необходимо учитывать, 
что иногда лучше что бы exception случился раньше, поэтому тоже будьте аккуратны.

### Extension functions
Следующее на что мы с вами посмотрим это extension functions.
Благодаря этой возможности вы можете расширять функционал любого класса не наследуясь от него.
Как пример выше была приведена функция `notNull`, она расширяет абсолютно любой класс.

Еще один пример, относящийся именно к android:

```kotlin
fun FragmentManager.replaceFragment(@IdRes layoutId: Int, fragment: Fragment) {
    beginTransaction()
            .disallowAddToBackStack()
            .replace(layoutId, fragment)
            .commit()
}

supportFragmentManager.replaceFragment(R.layout.main_container, SomeFragment())
```
Пример выше показывает, то что мы создали метод replaceFragment, который можно вызывать на объекте FragmentManager.
Тем самым мы расширили функционал FragmentManager, не наследуясь от него.
В Java extensions функции это просто static methods.

Для extensions функций есть в Kotlin интересное ключевое слово, а именно ```infix```.
Использовать это ключевое слово можно только тогда, когда у функции есть один параметр.
Пример с Toast:
```kotlin
infix fun Context.showToast(text: String) {
    Toast.makeText(this, text, Toast.LENGTH_SHORT).show()
}

context showToast "ALARM!"
```
Infix функции служат для повышения читаемости кода.

### Sealed classes
Отлично подходят для управления стейтом, так называемые Изолированные классы (sealed classes).
По сути их можно сравнить с enum, так как они тоже имеют ограниченный набор значений, 
то есть задать прямых наследников этого класса мы можем либо в самом классе, либо рядом с ним в том же файле.

```kotlin
sealed class State

class LoadingState(val someData: Data) : State()

class ContentState(val someContent: Content) : State()

class ErrorState(val someError: Error) : State()
```

В чем их преимущество по сравнению с enum?
А в том, что они имеют не один единственный экземпляр, как enum, а могут 
иметь множество экземпляров и при этом содержать какую либо информацию 
(например, текущая ошибка, или список каких либо элементов)
Это почти обычный класс, только с ограниченной иерархией.
Кстати, используя `sealed classes` с выражением `when`, вы можете не писать ветку else, 
так как `when` знает полностью иерархию этого класса.

```kotlin
sealed class Result
class Success(val response: Response) : Result()
class Failure(val error: Error) : Result()

when(state) {
    is Success -> state.response
    is Failure -> state.error
}
```

### Inline
Перейдем к inline functions. Это отличный функционал, который помогает не только писать красивый и лаконичный код,
но и при этом экономить. Inline functions подразумевают встраивание всего кода функции, 
а также переданной лямбды в то место откуда была вызвана эта inline function.

Необходимо уточнить тот момент, что это не означает того, что эта функция не будет учитываться 
в dex methods count, то есть количество методов не уменьшается, поэтому экономия связана не с этим.
Так в чем же сама экономия? Экономия в том, что для переданной в эту функцию лямбды не будет создаваться Анонимный класс, 
то есть код будет полностью встроен в то место откуда была вызвана функция. 
Также так как функция встраивается, то для нее не создается полноценный объект.

Давайте посмотрим на inline function:
```kotlin
inline fun printSomething(action: () -> String) {
    print(action())
}
```

Лямбда action из функции выше будет встроена, для нее не создается анонимный класс.
Если вы не хотите встраивать лямбду, то вам следует поставить перед лямбдой ключевое слово ```noinline``` 
Пример:
```kotlin
inline fun printSomething(noinline action: () -> String) {
    print(action())
}
```

Если вы захотите вызвать inline лямбду внутри другой лямбды, то для этого вам необходимо указать ключевое слово ```crossinline```
Пример:
```kotlin
inline fun printSomething(crossinline action: () -> String) {
    doSomething {
        action()
    }
}
```

В Kotlin 1.3 в виде экспериментальной фичи появились Inline classes. Для чего они?
Все очень просто, данный класс позволяет хранить в себе один field, то есть он будет выступать как wrapper над этим field.
Пример:
```kotlin
inline class AwesomeId(val id: Long)
```

Плюс этих классов в том, что в рантайме объект как таковой не создается, то есть остается только Long. 
Мы получаем типобезопасность и лучшее именование, без каких либо особых затрат.
То есть если раньше мы в один метод передавали 2 параметра типа ```long```, частым случаем было такое, 
что мы могли спутать их местами, с inline classes, такое сделать не получится.
В Kotlin также есть typeAlias, но в отличии от inline classes, он дает просто удобное именование, 
сам тип остается прежним, то есть:
```kotlin
typealias TestData = LongestNameForAwesomeDataWithManyFields
//or
typealias Id = Long

fun useTestData(testData: TestData) {
}

fun notifyAndSend(notifyId: Id, sendId: Id) {
}

```

И inline classes и typealias очень удобны. Допустим если вам просто нужно понятное именование, вы можете использовать typealias.
Если нужны полноценные типы и типобезопасность то однозначный выбор за inline classes. 
Также inline classes помогают делать перегрузку методов еще легче.

Необходимо также понимать, что есть случаи когда inline class будет выступать 
как полноценный wrapper и будет происходить autoboxing. В разделе `Что почитать` есть статья на эту тему, советую ознакомиться.


### Data classes

Что можно здесь сказать, это просто одно ключевое слово, которое дает вам автоматически сгенерированные компилятором методы, 
которые мы обычно создаем либо руками, либо с помощью libraries, либо через plugins.
Пример:
```kotlin
data class User(val id: Long, val name: String, val age: Int)
```

За нас автоматически сгенерируются такие методы как:
* equals()
* hashCode()
* toString()
* componentN() - необходимы для Destructuring Declarations
* copy()

Если какой то из этих методов переопределяется руками, сгенерирован он само собой не будет.
Пример как такое же сделать на Java если не использовать сторонние tools:
```java
public final class User {
    private final long id;
    @NotNull
    private final String name;
    private final int age;

    public User(long id, @NotNull String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public final long getId() {
        return id;
    }

    @NotNull
    public final String getName() {
        return name;
    }

    public final int getAge() {
        return age;
    }

    @NotNull
    public final User copy(long id) {
        return new User(id, name, age);
    }

    @NotNull
    public final User copy(long id, @NotNull String name) {
        return new User(id, name, age);
    }

    @NotNull
    public final User copy(long id, @NotNull String name, int age) {
        return new User(id, name, age);
    }

    @NotNull
    public String toString() {
        return "User(id=" + id + ", name=" + name + ", age=" + age + ")";
    }

    public int hashCode() {
        return ((int) (id ^ id >>> 32) * 31 + name.hashCode()) * 31 + this.age;
    }

    public boolean equals(@Nullable Object other) {
        if (this == other) {
            return true;
        }

        if (!(other instanceof  User)) {
            return false;
        }

        User user = (User) other;
        return id == user.id && name.equals(user.name) && age == user.age;
    }
}
```
Как видно выше, Data classes позволяют экономить приличное количество времени и строк кода.
А это еще не все варианты метода copy перегружены, и нет методов componentN, так как в Java это обычный getters.
Метод copy в Java конечно лучше заменить на pattern Builder.

А теперь о том, когда не стоит использовать Data classes. 
Так как мы android developers и мы живем с dex methods count, нам необходимо думать, 
когда стоит использовать ключевое слово data, а когда нет.
По сути здесь все просто, если вы не используете большую часть тех методов, 
что за вас генерируется, то и Data class вам не нужен. В крайнем случае equals/hashCode можно написать самому.

Все таки когда у вас есть метод, который вы перестали использовать, вы его просто удаляете, тут тот же самый смысл.

### Coroutines

А вот теперь повогорим с вами о многопоточности и о асинхронной работе.
Корутины как раз об этом, эта фича языка, которая призвана быть намного более приятной в использовании, 
чем те средства асинхронной работы, которые существуют на данный момент. 
Плюс Корутин в том, что их создание легче обычного Thread. То есть по сути корутина это обычная функция, 
которую можно приостановить, не блокируя при этом основной Thread, и заного запустить в определенный момент.
Под капотом у них state machine. Корутины не заменяют Thread, так как работают на их основе.

Основа всего в корутинах это ключевое слово в языке ```suspend```. Функции которые помечены этим ключевым словом, 
могут быть приостановлены, также необходимо знать, что такие функции могут быть вызваны только из coroutine builder.
Например:
```kotlin
suspend fun doSomething() {

}

fun main() {

    GlobalScope.launch {
        doSomething()
    }
    
    //или
    
    runBlocking {
        doSomething()
    }

}
```

Есть 2 основных типа билдера: 
* launch, возвращает Job.
* async, возврашает Deferred с результатом функции (как Future).

Основная идея корутин в том, что вы создаете корутину с помощью билдера допустим launch передаете ей Dispatcher, 
который будет управлять на каком потоке выполнять работу и все.
Если не передавать Dispatcher, то он будет выполняться на том потоке 
где была создана корутина или будет использовать Dispatcher из Scope.
Пример:
```kotlin
suspend fun doSomething() {

}

fun main() {

    //вы можете создать scope таким образом. Dispatcher в этом случае будет Executors с одним Thread. 
    val scope: CoroutineScope = CoroutineScope(Executors.newSingleThreadExecutor().asCoroutineDispatcher())
    // или так, здесь мы взяли готовый Dispatcher, а также задали parentJob, 
    // с помощью которой можно легко делать cancel для вложенных корутин.
    val scopeIo: CoroutineScope = CoroutineScope(Dispatchers.IO + SupervisorJob())
    
    //SupervisorJob отличается от обычной Job, тем, что приостановка child корутины, 
    // допустим из за Exception, никак не отражается на других child корутинах и parent

    scope.launch {
        doSomething()
    }
    
    // в таком случае будет использоваться именно тот Dispatcher который мы явно указали для этой корутины, 
    // то есть Dispatchers.Default
    scope.launch(Dispatchers.Default) {
        doSomething()
    }
}
```

В общем Scope, как и любой coroutine builder, принимает как параметр CoroutineContext, 
который может состоять не только из Dispatcher, но и из Job, как было показано выше, 
а также можно добавить CoroutineExceptionHandler.

Давайте теперь посмотрим на разницу между launch и async.
Первый builder, необходим для создания обычной корутины которую запустили и забыли.
Второй builder, необходим только тогда когда вам нужно выполнить 2 корутины параллельно.
Пример:
```kotlin
suspend fun loadRemoteData() {

}

suspend fun loadLocalData() {

}

fun prepareData(remoteData: Data, localData: Data) {

}

fun main() {   
    GlobalScope.launch {
        //создаем coroutine с Dispatcher default, для распределения вычеслений по ядрам процессора
        val remoteDataDeferred = async(Dispatchers.Default) {
            loadRemoteData()
        }
        
        //создаем coroutine с Dispatcher default, для распределения вычеслений по ядрам процессора
        val localDataDeferred = async(Dispatchers.Default) {
            loadLocalData()
        }
        
        //выполняться эти две корутины будут параллельно
        prepareData(remoteDataDeferred.await(), localDataDeferred.await())
    }
}
```
Необходимо также упомянуть про переключение контекста у корутины, делается это с помощью:
```kotlin
withContext(Dispatchers.IO) {
    
}
```
Эта функция не создает корутину, она лишь переключает context той корутины в которой была вызвана.

Корутины обширная тема, с каждым новым релизом добавляется, что то новое. 
Я советую подробно прочитать про все то, что я выше описал, а также про то, что не затронул, 
а именно такие темы как: channels, mutex и различные дополнительные возможности. 

## Что почитать

- https://kotlinlang.org/docs/reference/null-safety.html
- https://kotlinlang.org/docs/reference/extensions.html
- https://typealias.com/guides/introduction-to-inline-classes/
- https://typealias.com/guides/inline-classes-and-autoboxing/
- https://github.com/Kotlin/kotlinx.coroutines
- https://kotlinlang.org/docs/reference/coroutines/exception-handling.html
- https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines/-supervisor-job.html
- https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.channels/-channel/index.html
- https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.sync/-mutex/index.html
- https://proandroiddev.com/kotlin-coroutine-job-hierarchy-finish-cancel-and-fail-2d3d42a768a9
