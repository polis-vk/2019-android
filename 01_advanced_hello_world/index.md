---
layout: post
title: Создание Hello World приложения Advanced level
highlight: true
---

В этом уроке мы продолжим работать с приложение Hello World: немного усложним верстку и добавим к нему интерактивности. Более внимательно рассмотрим структуру проекта и найдем в нем кое-что лишнее и ненужное, научимся запускать приложение на реальном устройстве вместо эмулятора, а также научимся обходиться без Android Studio для некоторых задач разработки. Бонус -- после завершения этого урока вы станете настоящим Android разработчиком (я обещаю).

## Задача

Мы хотим сделать приложение, в котором вместо приветствимя *"Hello, World!"* на экране будет отображаться персонализированное приветствие с именем пользователя вида *"Hello, &lt;username>!"*. Для этого мы добавим на экран
поле ввода, куда пользователь будет вводить своё имя и кнопку для подтверждения ввода, по нажатию на которую
и будет выводиться текст сообщения с именем пользователя.

## Добавление второй активности

Создайте новый класс активности в том же Java пакете и назовите его `HelloUsernameActivity`. Пусть этот класс также наследует от `android.app.Activity`. UI этой активности будет отличаться от `HelloWorldActivity`, но для начала, чтобы просто убедиться, что вторая активность работает, скопируйте файл верстки `activity_hello_world` в `activity_hello_username` и загрузите этот файл верстки в методе `onCreate` класса `HelloUsernameActivity`:

```
public class HelloUsernameActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_hello_username);
    }
}
```

Для того, чтобы экраны визуально отличались, замените строковый ресурс `hello_world` на новый ресурс `hello_username` в файле верстки `activity_hello_username`:
```
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_gravity="center"
    android:text="@string/hello_username"/>
```

Добавьте новый строковый ресурс `hello_username` в файл `app/src/main/res/values/strings.xml` с каким-нибудь уникальным значением. Сейчас файл `strings.xml` должен выглядеть приблизительно так:

```
<resources>
    <string name="app_name">HelloWorld</string>
    <string name="hello_world">Hello, World!</string>
    <string name="hello_username">Hello, &lt;Username&gt;!</string>
</resources>
```

Как мы уже знаем, все активности приложения обязательно должны быть прописаны в манифесте. Откройте файл `app/src/main/AndroidManifest.xml` и добавьте внутрь `application` еще один элемент `activity` для нашей новой активности `HelloUsernameActivity`:
```
    <activity android:name=".HelloUsernameActivity">
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>

```

Здесь мы прописали для нашей второй активности action=MAIN и category=LAUNCHER -- так же, как для первой активности `HelloWorldActivity`. Как мы помним, такие параметры активности означают, что это основная точка входа в приложение, и для неё будет создана иконка на домашнем экране смартфона. Стоп! Как могут быть две основные точки входа в приложение?...  Оказывается, могут. Основная не значите единственная. Что именно это значит, мы обсудим чуть позже в этом уроке, а пока запустим приложение на эмуляторе.

После старта приложения мы, как и раньше, увидим экран ссобщением "Hello, World", а вот если закрыть этот экран и посмотреть на список приложений в домашнем экране эмулятора, то мы увидим, что теперь в списке есть два одинаковых ярлыка с одинаковым названием "HelloWorld" -- это и есть две наших активности, которые обе приписаны в манифесте, как MAIN.

<img src="img/0500_two_main_activities.png" width="400px"/>

Ярлыки имеют одинаковые иконки и названия, потому что в манифесте у активноестей не указаны никакие специальные иконки и названия, и поэтому используются значения `icon` (`roundIcon`) и `label` элемента `application` манифеста:
```
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
```

Чтобы различать ярлыки активностей, создайте для них в файле `app/src/main/res/values/strings.xml` строковые ресурсы с уникальными именами:

```
    <string name="activity_hello_world">Hello World</string>
    <string name="activity_hello_user">Hello User</string>
```

и используйте их в качестве значения атрибута `label` в манифесте:
```
    <activity android:name=".HelloWorldActivity"
              android:label="@string/activity_hello_world">
		...

    <activity android:name=".HelloUsernameActivity"
              android:label="@string/activity_hello_user">
		...
```

Теперь в списке приложений на домашнем экране яплыки будут иметь разные названия, и мы сможем различать наши две активности:

<img src="img/0510_two_main_activities.png" width="400px"/>

Осталась последняя проблема -- в нашем Hello World приложении сейчас есть два экрана, и мы можем запускать любой из них, выбирая нужную иконку в домашнем экране смартфона, но при запуске из Android Studio будет всегда запускаться экран Hello World. Мы же хотим сейчас сфокусироваться на разработке экрана Hello User, и было бы удобно, чтобы при запуске из Android Studio запускался именно он. Для это добавьте категорию `DEFAULT` в `intent-filter` активности `HelloUsernameActivity` в манифесте:

```
    <activity android:name=".HelloUsernameActivity"
              android:label="@string/activity_hello_user">
        <intent-filter>
            <action android:name="android.intent.action.MAIN"/>
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.LAUNCHER"/>
        </intent-filter>
    </activity>
```

Категория DEFAULT говорит о том, что если для выполнения какого-то действия внутри одного приложения есть несколько вариантов (в данном случае -- две разные активности MAIN), то система выберет то действие, которое имеет категорию DEFAULT. Android Studio, которая выполняет запуск нашего приложения на эмуляторе по нажатию на кнопку Run, тоже понимает манифест приложения и запускает активность с категорией DEFAULT.

Есть другой способ сказать Android Studio, какую активность запускать по нажатию на Run: в настройках конфигурации запуска (Чтобы открыть настройки конфигураций запуска, выберите *Edit Configurations* в drop-down списке слева от кнопки Run в верхней панели инструментов) есть раздел *Launch Options* -- там можно выбрать вариант *Specified Activity* и затем выбрать какую-то конкретную активность для запуска.

<img src="img/0520_edit_launch_options.png" width="1200px"/>

Разница между этими вариантами заключается в том, что категория DEFAULT у одного из двух равнозначных варинатов -- это свойство самого приложения, известное операционной системе и другим приложениям. А настройки Android Stuidio -- это... всего лишь её настройки.

## Верстка экрана Hello User

Сейчас мы сделаем верстку экрана Hello User с интерактивными элементами, которая выглядит вот так:

<img src="img/0530_hello_user_layout.png" width="400px"/>

Сначала идет статический текст "Меня зовут", приглашающий пользователя ввести своё имя, потом поле ввода имени, кнопка подтверждения "Привет" и текст приветствия, который должен содержать введённое пользователем имя вместо *&lt;Username&gt;*.

Откройте файл верстки `activity_hello_username` и отредактируйте его, чтобы он выглдядел так:

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/my_name_is"
        android:textSize="18sp"/>

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/hi"/>

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/hello_username"
        android:textSize="18sp"/>

</LinearLayout>
```

В этой верстке есть несколько новых моментов:

- В качестве корневого элемента теперь используется `LinearLayout`, а не `FrameLayout`. FrameLayout слишком простой -- он хорош для того, чтобы просто отобразить что-то внутри себя, но не может упорядочить View. LinearLayout позволяет отображать View один за другим по вертикали или по горизонтали. В данном случае -- по вертикали, о чем говорит атрибут `orientation="vertical"`.
- У элементов `TextView` используется атрибут `textSize` для изменения размера текста (чтобы текст на скриншоте выше было лучше видно). Значение этого атрибута `18sp` использует единицу измерения `sp` (scaled pixels). Эта единица измерения используется обычно для текста, и её особенность заключается в том, что она учитывает системные настройки размера экранных текстов.
- Для поля ввода используется `EditText` -- это стандартный системный класс `android.widget.EditText`.
- Для кнопки используется `Button` -- это `android.widget.Button`
- `Button` являются подклассом `TextView` (так же, как и `EditText`), поэтому у него есть атрибут `text`, который здесь используется для надписи на кнопке.

В верстке использованы новые строковые ресурсы `my_name_is` и `hi` -- добавьте их в файл со строковыми ресурсами `app/src/main/res/values/strings.xml` и не забудьте про переводы на русский язык в `app/src/main/res/values-ru/strings.xml`.

## Поиск View по ID

Мы собираемся добавить код, который будет обрабатывать клик на `Button` и использовать ввод из `EditText`. Но прежде, чем мы сможем писать какую-то логику работы с View в коде, нам нужно получить доступ к этим View из кода, потому что на данный момент нужные нам View определены только в файле вёрстки. Для связи кода с вёрсткой нам помогут View ID.

У класса `View` есть свойство `id` типа `int`, которое доступно разработчикам через сеттер `View.setId(int id)` и геттер `int View.getId()`. По умолчанию View имеет нулевой ID: `getId() == 0`, но в файле вёрски можно указать какое-то значение атрибута `id`, и View получит отличный от нуля идентификатор. Делается это при помощи следующего синтаксиса:
```
<View
    android:id="@+id/unique_id_name"
	android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>
```

Здесь используется специальный синтаксис ссылки на ID ресурса со знаком плюс: `@+id/unique_id_name`. Знак плюс означает, что мы не ссылаемся на какой-то ресурс, который определен в другом места (как мы делали со строками), а определяем его прямо здесь по месту использования. Если бы мы использовали синтаксис без плюса, до произошла бы ошибка сборки из-за того, что мы ссылаемся на ресурс, который нигде не определен. Однако, если ID какого-то View уже был однажды определен в верстке, то в других местах можно использовать синтаксис ссылки на этот ID без плюса -- в случаях, когда мы явно хотим сказать: "Где-то должен  быть такой ID, и мы хотим использовать именно его". Пример:
```
<View
    android:id="@+id/unique_id_name"
	android:layout_width="wrap_content"
    android:layout_height="wrap_content"/>

<View
	android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:layout_constraintLeftToRightOf="@id/unique_id_name"/>
```

Для каждого ID, определенного таким образом в файле верстки, будет сгенерирована константа `R.id.unique_id_name`, котороую можно использовать в коде для работы с View. Самый частый кейс использования таких констант -- поиск View по ID в загруженной из файла верстки иерархии View. Для этого у класса `Activity` есть метод:
```
@Nullable
public <T extends View> T findViewById(@IdRes int id);
```
Он принимает в качестве аргумента ID (Аннотация `@IdRes` говорит, что это должно быть не произвольное значение, а какая-то константа из `R.id`), и возвращает инстанс `View` с таким ID из ранее загруженной иерархии View. Если `View` с таким ID не найден, то метод вернет `null`. Очевидно, использовать этот метод надо после вызова `setContentView`, когда иерархия View уже загружена.

*На заметку №1: метод `findViewById` реализован методом DFS. Возомжна ситуация, в которой разные View в иерархии имеют одинаковые ID -- в таком случае будет найдено первое View в порядке обхода DFS.*

*На заметку №2: использование констант ресурсов из `R.id` в качестве View ID не является строго обязательным. Технически возможно использовать произвольные значения, устанавливая их из кода сеттером `View.setId`. Однако, делать это не рекомендуется, потому что в андроидном UI движке View ID используются повсеместно, и неправильное их использование может что-нибудь сломать. Опасаться следует в первую очередь непредвиденных коллизий ID там, где их быть не должно.*

Теперь добавьте ID в верстку. ID нужны только у тех View, с которыми мы будем работать в коде:
```
<EditText
    android:id="@+id/input_name"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>

<Button
    android:id="@+id/btn_submit"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/hi"/>

<TextView
    android:id="@+id/message_text"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/hello_username"
    android:textSize="18sp"/>
```