---
layout: post
title: "Attaching functions to types"
description: "Creating methods the F# way"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 11
---

> Although we have focused on the pure functional style so far, sometimes it is convenient to switch to an object oriented style.
And one of the key features of the OO style is the ability to attach functions to a class and "dot into" the class to get the desired behavior.

Хотя до этого повествование было сфокусировано на чисто функциональном стиле, иногда удобно переключиться на объектно-ориентированный стиль. А одной из ключевых особенностей объектно-ориентированного стиля является возможность прикреплять функции к классу и обращение к классу через точку для получение желаемого поведения.

> In F#, this is done using a feature called "type extensions".  And any F# type, not just classes, can have functions attached to them.

В F# это возможно с помощью фичи, которая называется "расширение типов" ("type extensions"). У любого F# типа, не только класса, могут быть прикреплённые функции.

> Here's an example of attaching a function to a record type.

Вот пример прикрепления функции к типу записи.

```fsharp
module Person =
    type T = {First:string; Last:string} with
        // функция-член, объявленная вместе с типом
        member this.FullName =
            this.First + " " + this.Last

    // конструктор
    let create first last =
        {First=first; Last=last}

let person = Person.create "John" "Doe"
let fullname = person.FullName
```

> The key things to note are:

Ключевые моменты, на которые следует обратить внимание:

> * The `with` keyword indicates the start of the list of members
> * The `member` keyword shows that this is a member function (i.e. a method)
> * The word `this` is a placeholder for the object that is being dotted into (called a "self-identifier"). The placeholder prefixes the function name, and then the function body then uses the same placeholder when it needs to refer to the current instance.
There is no requirement to use a particular word, just as long as it is consistent. You could use `this` or `self` or `me` or any other word that commonly indicates a self reference.

* Ключевое слово `with` обозначает начало списка членов
* Ключевое слово `member` показывает, что функция является членом (т.е. методом)
* Слово `this` является меткой объекта, на котором вызывается данный метод (также называемая "self-identifier"). Это слово является префиксом имени функции, и внутри функции можно использовать его для обращения к текущему экземпляру. Не существует требований к словам, используемым в качестве самоидентификатора, достаточно чтобы они были устойчивы. Можно использовать `this`, `self`, `me` или любое другое слово, которое обычно используется как отсылка на самого себя.

Нет нужды добавлять член вместе с объявлением типа, всегда можно добавить его позднее в том же модуле:

```fsharp
module Person =
    type T = {First:string; Last:string} with
       // член, объявленный вместе с типом
        member this.FullName =
            this.First + " " + this.Last

    // конструктор
    let create first last =
        {First=first; Last=last}

    // другой член, объявленный позже
    type T with
        member this.SortableName =
            this.Last + ", " + this.First

let person = Person.create "John" "Doe"
let fullname = person.FullName
let sortableName = person.SortableName
```

> These examples demonstrate what are called "intrinsic extensions". They are compiled into the type itself and are always available whenever the type is used. They also show up when you use reflection.

Эти примеры демонстрируют вызов "встроенных расширений" ("intrinsic extensions"). Они компилируются в тип и будут доступны везде, где бы тип ни использовался. Они также будут показаны при использовании рефлексии.

> With intrinsic extensions, it is even possible to have a type definition that divided across several files, as long as all the components use the same namespace and are all compiled into the same assembly.
Just as with partial classes in C#, this can be useful to separate generated code from authored code.

Внутренние расширения позволяют даже разделять определение типа на несколько файлов, пока все компоненты используют одно и то же пространство имён и компилируются в одну сборку. Так же как и с partial классами в C#, это может быть полезным для разделения сгенерированного и написанного вручную кода.

## Optional extensions | Опциональные расширения

> Another alternative is that you can add an extra member from a completely different module.
> These are called "optional extensions". They are not compiled into the type itself, and require some other module to be in scope for them to work (this behavior is just like C# extension methods).

Альтернативный вариант заключается в том, что можно добавить дополнительный член из совершенно другого модуля. Их называют "опциональными расширениями". Они не компилируются внутрь класса, и требуют другой модуль в области видимости для работы с ними (данное поведение напоминает методы-расширения из C#).

> For example, let's say we have a `Person` type defined:

Например, пусть определен тип `Person`:

```fsharp
module Person =
    type T = {First:string; Last:string} with
        // член, объявленный вместе с типом
        member this.FullName =
            this.First + " " + this.Last

    // конструктор
    let create first last =
        {First=first; Last=last}

    // ещё один член, объявленный позже
    type T with
        member this.SortableName =
            this.Last + ", " + this.First
```

> The example below demonstrates how to add an `UppercaseName` extension to it in a different module:

Пример ниже демонстрирует, как можно добавить расширение `UppercaseName` к нему в другом модуле:

```fsharp
// в другом модуле
module PersonExtensions =

    type Person.T with
    member this.UppercaseName =
        this.FullName.ToUpper()
```

> So now let's test this extension:

Теперь можно попробовать это расширение:

```fsharp
let person = Person.create "John" "Doe"
let uppercaseName = person.UppercaseName
```

> Uh-oh, we have an error. What's wrong is that the `PersonExtensions` is not in scope. 
> Just as for C#, any extensions have to be brought into scope in order to be used.

Упс, получаем ошибку. Она произошла потому, что `PersonExtensions` не находится в области видимости. Как и в C#, чтобы использовать любые расширения, их нужно ввести в область видимости.

> Once we do that, everything is fine:

Как только мы сделаем это, все заработает:

```fsharp
// Сначала сделаем расширение доступным!
open PersonExtensions

let person = Person.create "John" "Doe"
let uppercaseName = person.UppercaseName
```

## Extending system types | Расширение системных типов

> You can extend types that are in the .NET libraries as well. But be aware that when extending a type, you must use the actual type name, not a type abbreviation. 

Можно также расширять типы из .NET библиотек. Но следует иметь ввиду, что при расширении типа надо использовать его фактическое имя, а не псевдоним.

> For example, if you try to extend `int`, you will fail, because `int` is not the true name of the type:

Например, если попробовать расширить `int`, ничего не получится, т.к. `int` не является правильным именем для типа:

```fsharp
type int with
    member this.IsEven = this % 2 = 0
```

> You must use `System.Int32` instead:

Вместо этого нужно использовать `System.Int32`:

```fsharp
type System.Int32 with
    member this.IsEven = this % 2 = 0

let i = 20
if i.IsEven then printfn "'%i' is even" i
```

## Static members | Статические члены

> You can make the member functions static by:

Можно создавать статические функции-члены с помощью:

> * adding the keyword `static` 
> * dropping the `this` placeholder

* добавления ключевого слова `static`
* удаления метки `this`

```fsharp
module Person =
    type T = {First:string; Last:string} with
        // член, определённый вместе с типом
        member this.FullName =
            this.First + " " + this.Last

        // статический конструктор
        static member Create first last =
            {First=first; Last=last}

let person = Person.T.Create "John" "Doe"
let fullname = person.FullName
```

> And you can create static members for system types as well:

Можно создавать статические члены для системных типов:

```fsharp
type System.Int32 with
    static member IsOdd x = x % 2 = 1

type System.Double with
    static member Pi = 3.141

let result = System.Int32.IsOdd 20
let pi = System.Double.Pi
```

<a name="attaching-existing-functions" ></a>
## Attaching existing functions | Прикрепление существующих функций

> A very common pattern is to attach pre-existing standalone functions to a type.  This has a couple of benefits:

Очень распространённый паттерн - прикрепление уже существующих самостоятельных функций к типу. Он даёт несколько преимуществ:

> * While developing, you can create standalone functions that refer to other standalone functions. This makes programming easier because type inference works much better with functional-style code than with OO-style ("dotting into") code.
> * But for certain key functions, you can attach them to the type as well. This gives clients the choice of whether to use functional or object-oriented style.

* Во время разработки можно объявлять самостоятельные функции, которые ссылаются на другие самостоятельные функции. Это упростит разработку, поскольку вывод типов гораздо лучше работает с функциональным стилем, нежели с объектно-ориентированным ("через точку").
* Но некоторые ключевые функции можно прикрепить к типу. Что позволит пользователям выбрать, какой из стилей использовать, функциональный или объектно-ориентированный.

> One example of this in the F# libraries is the function that calculates a list's length. It is available as a standalone function in the `List` module, but also as a method on a list instance.

Примером подобного решения является функция из F# библиотеки, которая вычисляет длину списка. Можно использовать самостоятельную функцию из модуля `List` или вызывать ее как метод экземпляра.

```fsharp
let list = [1..10]

// функциональный стиль
let len1 = List.length list

// объектно-ориентированный стиль
let len2 = list.Length
```

> In the following example, we start with a type with no members initially, then define some functions, then finally attach the `fullName` function to the type.

В следующем примере тип изначально не имеет каких-либо членов, затем определяются несколько функций, и наконец к типу прикрепляется функция `fullName`.

```fsharp
module Person =
    // тип, изначально не имеющий членов
    type T = {First:string; Last:string}

    // конструктор
    let create first last =
        {First=first; Last=last}

    // самостоятельная функция
    let fullName {First=first; Last=last} =
        first + " " + last

    // присоединение существующей функции в качестве члена
    type T with
        member this.FullName = fullName this

let person = Person.create "John" "Doe"
let fullname = Person.fullName person  // ФП
let fullname2 = person.FullName        // ООП
```

> The standalone `fullName` function has one parameter, the person. In the attached member, the parameter comes from the `this` self-reference.

Самостоятельная функция `fullName` имеет один параметр, `person`. Присоединённый же член получает параметр из self-ссылки.

### Attaching existing functions with multiple parameters | Добавление существующих функций с несколькими параметрами

> One nice thing is that when the previously defined function has multiple parameters, you don't have to respecify them all when doing the attachment, as long as the `this` parameter is first.

Есть ещё одна приятная особенность. Если определённая ранее функция принимает несколько параметров, то когда вы будете прикреплять её к типу, вам не придётся перечислять все эти параметры снова. Достаточно указать параметр `this` первым.

> In the example below, the `hasSameFirstAndLastName` function has three parameters. Yet when we attach it, we only need to specify one! 

В примере ниже функция `hasSameFirstAndLastName` имеет три параметра. Однако при прикреплении достаточно упомянуть всего лишь один!

```fsharp
module Person =
    // Тип без членов
    type T = {First:string; Last:string}

    // конструктор
    let create first last =
        {First=first; Last=last}

    // самостоятельная функция
    let hasSameFirstAndLastName (person:T) otherFirst otherLast =
        person.First = otherFirst && person.Last = otherLast

    // присоединение функции в качестве члена
    type T with
        member this.HasSameFirstAndLastName = hasSameFirstAndLastName this

let person = Person.create "John" "Doe"
let result1 = Person.hasSameFirstAndLastName person "bob" "smith" // ФП
let result2 = person.HasSameFirstAndLastName "bob" "smith" // ООП
```

> Why does this work? Hint: think about currying and partial application!

Почему это работает? Подсказка: подумайте о каррировании и частичном применении!

<a name="tuple-form" ></a>
## Tuple-form methods | Кортежные методы

> When we start having methods with more than one parameter, we have to make a decision:

Когда у нас появляются методы с более чем одним параметром, необходимо принять решение:

> * we could use the standard (curried) form, where parameters are separated with spaces, and partial application is supported.
> * we could pass in *all* the parameters at once, comma-separated, in a single tuple.

* мы можем использовать стандартную (каррированную) форму, где параметры разделяются пробелами, и поддерживается частичное применение.
* или можем передавать *все* параметры за один раз в виде разделённого запятыми кортежа.

> The "curried" form is more functional, and the "tuple" form is more object-oriented.

Каррированая форма более функциональная, в то время как кортежная форма более объектно-ориентированная.

> The tuple form is also how F# interacts with the standard .NET libraries, so let's examine this approach in more detail.

Кортежная форма также используется для взаимодействия F# со стандартными библиотеками .NET, поэтому стоит рассмотреть данный подход более детально.

> As a testbed, here is a Product type with two methods, each implemented using one of the approaches.
The `CurriedTotal` and `TupleTotal` methods each do the same thing: work out the total price for a given quantity and discount.

Нашим испытательным полигоном будет тип `Product` с двумя методами, каждый из которых реализован одним из способов, описанных выше. Методы `CurriedTotal` и `TupleTotal` делают одно и то же: вычисляют итоговую стоимость товара по заданным количеству и скидке.

```fsharp
type Product = {SKU:string; Price: float} with

    // каррированная форма
    member this.CurriedTotal qty discount =
        (this.Price * float qty) - discount

    // кортежная форма
    member this.TupleTotal(qty,discount) =
        (this.Price * float qty) - discount
```

> And here's some test code:

Тестовый код:

```fsharp
let product = {SKU="ABC"; Price=2.0}
let total1 = product.CurriedTotal 10 1.0
let total2 = product.TupleTotal(10,1.0)
```

> No difference so far.

Пока нет особой разницы.

> We know that curried version can be partially applied:

Но мы знаем, что каррированная версия может быть частично применена:

```fsharp
let totalFor10 = product.CurriedTotal 10
let discounts = [1.0..5.0]
let totalForDifferentDiscounts
    = discounts |> List.map totalFor10
```

> But the tuple approach can do a few things that that the curried one can't, namely:

С другой стороны, кортежная версия способна на то, что не может каррированая, а именно:

> * Named parameters
> * Optional parameters
> * Overloading

* Именованные параметры
* Необязательные параметры
* Перегрузки

### Named parameters with tuple-style parameters | Именованные параметры с параметрами в форме кортежа

> The tuple-style approach supports named parameters:

Кортежний подход поддерживает именованные параметры:

```fsharp
let product = {SKU="ABC"; Price=2.0}
let total3 = product.TupleTotal(qty=10,discount=1.0)
let total4 = product.TupleTotal(discount=1.0, qty=10)
```

> As you can see, when names are used, the parameter order can be changed.  

Как видите, это позволяет менять порядок аргументов с помощью явного указания имен.

> Note: if some parameters are named and some are not, the named ones must always be last.

Внимание: если лишь у некоторой части параметров есть имена, то эти параметры всегда должна находиться в конце.

### Optional parameters with tuple-style parameters | Необязательные параметры с параметрами в форме кортежа

> For tuple-style methods, you can specify an optional parameter by prefixing the parameter name with a question mark.

Для методов с параметрами в форме кортежа можно помечать параметры как опциональные при помощи префикса в виде знака вопроса перед именем параметра.

> * If the parameter is set, it comes through as `Some value`
> * If the parameter is not set, it comes through as `None`

* Если параметр задан, то в функцию будет передано `Some value`
* Иначе придет `None`

> Here's an example:

Пример:

```fsharp
type Product = {SKU:string; Price: float} with

    // Опциональная скидка
    member this.TupleTotal2(qty,?discount) =
        let extPrice = this.Price * float qty
        match discount with
        | None -> extPrice
        | Some discount -> extPrice - discount
```

> And here's a test:

И тест:

```fsharp
let product = {SKU="ABC"; Price=2.0}

// скидка не передана
let total1 = product.TupleTotal2(10)

// скидка передана
let total2 = product.TupleTotal2(10,1.0)
```

> This explicit matching of the `None` and `Some` can be tedious, and there is a slightly more elegant solution for handling optional parameters.

Явная проверка на `None` и `Some` может быть утомительной, но для обработки опциональных параметров существует более элегантное решение.

> There is a function `defaultArg` which takes the parameter as the first argument and a default for the second argument. If the parameter is set, the value is returned.
> And if not, the default value is returned.

Существует функция `defaultArg`, которая принимает имя параметра в качестве первого аргумента и значение по умолчанию в качестве второго. Если параметр установлен, будет возвращено соответствующее значение, иначе - значение по умолчанию.

> Let's see the same code rewritten to use `defaultArg` 

Тот же код с применением `defaulArg`:

```fsharp
type Product = {SKU:string; Price: float} with

    // опциональная скидка
    member this.TupleTotal2(qty,?discount) = 
        let extPrice = this.Price * float qty
        let discount = defaultArg discount 0.0
        extPrice - discount
```

<a id="method-overloading"></a>

### Method overloading | Перегрузка методов

> In C#, you can have multiple methods with the same name that differ only in their function signature (e.g. different parameter types and/or number of parameters)

В C# можно создать несколько методов с одинаковым именем, которые отличаются своей сигнатурой (например, различные типы параметров и/или их количество).

> In the pure functional model, that does not make sense -- a function works with a particular domain type and a particular range type. 
> The same function cannot work with different domains and ranges.  

В чисто функциональной модели это не имеет смысла -- функция работает с конкретным типом аргумента (domain) и конкретным типом возвращаемого значения (range). Одна и та же функция не может взаимодействовать с другими domain и range.

> However, F# *does* support method overloading, but only for methods (that is functions attached to types) and of these, only those using tuple-style parameter passing.

Однако, F# поддерживает перегрузку методов, но только для методов (которые прикреплены к типам) и только тех из них, которые написаны в кортежном стиле.

> Here's an example, with yet another variant on the `TupleTotal` method!

Вот пример с еще одним вариантом метода `TupleTotal`!

```fsharp
type Product = {SKU:string; Price: float} with

    // без скидки
    member this.TupleTotal3(qty) =
        printfn "using non-discount method"
        this.Price * float qty

    // со скидкой
    member this.TupleTotal3(qty, discount) =
        printfn "using discount method"
        (this.Price * float qty) - discount
```

> Normally, the F# compiler would complain that there are two methods with the same name, but in this case, because they are tuple based and because their signatures are different, it is acceptable.
> (To make it obvious which one is being called, I have added a small debugging message.)

Как правило компилятор F# ругается на то, что существует два метода с одинаковым именем, но в данном случае это приемлемо, т.к. они объявлены в кортежной нотации и их сигнатуры различаются. (Чтобы было понятно, какой из методов вызывается, я добавил небольшие сообщения для отладки)

> And here's a test:

Пример использования:

```fsharp
let product = {SKU="ABC"; Price=2.0}

// скидка не указана
let total1 = product.TupleTotal3(10)

// скидка указана
let total2 = product.TupleTotal3(10,1.0)
```

<a id="downsides-of-methods"></a>

## Hey! Not so fast... The downsides of using methods | Эй! Не так быстро... Недостатки использования методов

> If you are coming from an object-oriented background, you might be tempted to use methods everywhere, because that is what you are familiar with.
> But be aware that there some major downsides to using methods as well:

Придя из объектно-ориентированного мира, можно поддаться соблазну использовать методы везде, потому что это что-то привычное. Но следует быть осторожным, т.к. у них существует ряд серьезных недостатков:

> * Methods don't play well with type inference
> * Methods don't play well with higher order functions

* Методы плохо работают с выводом типов
* Методы плохо работают с функциями высшего порядка

> In fact, by overusing methods you would be needlessly bypassing the most powerful and useful aspects of programming in F#.

На самом деле, злоупотребляя методами, можно упустить самые сильные и полезные стороны программирования на F#.

> Let's see what I mean.

Посмотрим, что я имею ввиду.

### Methods don't play well with type inference | Методы плохо взаимодействуют с выводом типов

> Let's go back to our Person example again, the one that had the same logic implemented both as a standalone function and as a method:

Вернемся к примеру с `Person`, в котором одна и та же логика была реализована в самостоятельной функции и в методе:

```fsharp
module Person =
    // тип без методов
    type T = {First:string; Last:string}

    // конструктор
    let create first last =
        {First=first; Last=last}

    // самостоятельная функция
    let fullName {First=first; Last=last} =
        first + " " + last

    // функция-член
    type T with
        member this.FullName = fullName this
```

> Now let's see how well each one works with type inference.  Say that I want to print the full name of a person, so I will define a function `printFullName` that takes a person as a parameter.

Теперь посмотрим, насколько хорошо вывод типов работает с каждым из способов. Допустим, я хочу вывести полное имя человека, тогда я определю функцию `printFullName`, которая принимает `person` в качестве параметра.

> Here's the code using the module level standalone function.

Код, использующий самостоятельную функцию из модуля:

```fsharp
open Person

// использование самостоятельной функции
let printFullName person =
    printfn "Name is %s" (fullName person)

// Сработал вывод типов
//    val printFullName : Person.T -> unit
```

> This compiles without problems, and the type inference has correctly deduced that parameter was a person

Компилируется без проблем, а вывод типов корректно идентифицирует параметр как `Person`.

> Now let's try the "dotted" version:

Теперь попробуем версию через точку:

```fsharp
open Person

// обращение к методу "через точку"
let printFullName2 person =
    printfn "Name is %s" (person.FullName)
```

> This does not compile at all, because the type inference does not have enough information to deduce the parameter. *Any* object might implement `.FullName` -- there is just not enough to go on.

Этот код вообще не скомпилируется, т.к. вывод типов не имеет достаточной информации, чтобы определить тип параметра. *Любой* объект может реализовывать `.FullName` -- этого недостаточно для вывода.

> Yes, we could annotate the function with the parameter type, but that defeats the whole purpose of type inference.

Да, мы можем аннотировать функцию типом параметра, но из-за этого теряется весь смысл автоматического вывода типов.

### Methods don't play well with higher order functions | Методы плохо сочетаются с функциями высшего порядка

> A similar problem happens with higher order functions. For example, let's say that, given a list of people, we want to get all their full names.

Подобная проблема возникает и в функциях высшего порядка. Например, есть список людей, и нам надо получить список их полных имен.

> With a standalone function, this is trivial:

В случае самостоятельной функции решение тривиально:

```fsharp
open Person

let list = [
    Person.create "Andy" "Anderson";
    Person.create "John" "Johnson";
    Person.create "Jack" "Jackson"]

// получение всех полных имён
list |> List.map fullName
```

> With object methods, we have to create special lambdas everywhere:

В случае метода объекта, придется везде создавать специальную лямбду:

```fsharp
open Person

let list = [
    Person.create "Andy" "Anderson";
    Person.create "John" "Johnson";
    Person.create "Jack" "Jackson"]

// получение всех имён
list |> List.map (fun p -> p.FullName)
```

> And this is just a simple example. Object methods don't compose well, are hard to pipe, and so on.

А ведь это еще достаточно простой пример. Методы объектов довольно поддаются композиции, неудобны в конвейере и т.д.

> So, a plea for those of you new to functionally programming. Don't use methods at all if you can, especially when you are learning.
> They are a crutch that will stop you getting the full benefit from functional programming.

Поэтому, если вы новичок в функциональном программировании, то призываю вас: если можете, не используйте методы, особенно в процессе обучения. Они будут костылем, который не позволит извлечь из функционального программирования максимальную выгоду.