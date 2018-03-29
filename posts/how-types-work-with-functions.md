---
layout: post
title: "How types work with functions"
description: "Understanding the type notation"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 4
categories: [Types, Functions]
---

> Now that we have some understanding of functions, we'll look at how types work with functions, both as domains and ranges. This is just an overview; the series ["understanding F# types"](../series/understanding-fsharp-types.md) will cover types in detail. 

Теперь, когда у нас есть некоторое понимание функций, мы посмотрим как типы взаимодействуют с функциями, как domain-а, так и range. Это просто обзор, серия ["understanding F# types"](../series/understanding-fsharp-types.md) раскроет типы более подробно. 

> First, we need to understand the type notation a bit more. We've seen that the arrow notation "`->`" is used to show the domain and range. So that a function signature always looks like:

Сперва, нам надо чуть лучше понять нотацию типов. Мы видели стрелочную нотацию "`->`" разделяющую domain и range. Так что сигнатура функций всегда выглядит вот так:

```fsharp
val functionName : domain -> range
```

> Here are some example functions:

Еще несколько примеров функций:

```fsharp
let intToString x = sprintf "x is %i" x  // format int to string
let stringToInt x = System.Int32.Parse(x)
```

> If you evaluate that in the F# interactive window, you will see the signatures: 

> Если выполнить этот код в интерактивном окне, можно увидеть следующие сигнатуры:

```fsharp
val intToString : int -> string
val stringToInt : string -> int
```

> This means:

Они означают:

> * `intToString` has a domain of `int` which it maps onto the range `string`.
> * `stringToInt` has a domain of `string` which it maps onto the range `int`. 

* `intToString` имеет domain типа `int`, который сопоставляется с range типа `string`.
* `stringToInt` имеет domain типа `string`, который сопоставляется с range типа `int`.

##  Primitive types | Примитивные типы ##

> The possible primitive types are what you would expect:  string, int, float, bool, char, byte, etc., plus many more derived from the .NET type system.

Есть ожидаемые примитивные типы: string, int, float, bool, char, byte, etc., а также еще множество других производных от системы типов .NET.

> Here are some more examples of functions using primitive types:

Функции с примитивными типами:

```fsharp
let intToFloat x = float x // "float" fn. converts ints to floats
let intToBool x = (x = 2)  // true if x equals 2
let stringToString x = x + " world"
```

> and their signatures are:

и их сигнатуры:

```fsharp
val intToFloat : int -> float
val intToBool : int -> bool
val stringToString : string -> string
```

## Type annotations | Аннотация типов ##

> In the previous examples, the F# compiler correctly determined the types of the parameters and results. But this is not always the case. If you try the following code, you will get a compiler error:

В предыдущих примерах компилятор F# корректно определял типы параметров и результатов. Но так бывает не всегда. Если попробовать выполнить следующий код,  будет получена ошибка компиляции:

```fsharp
let stringLength x = x.Length         
   => error FS0072: Lookup on object of indeterminate type
```

> The compiler does not know what type "x" is, and therefore does not know if "Length" is a valid method. In most cases, this can be fixed by giving the F# compiler a "type annotation" so that it knows which type to use. In the corrected version below, we indicate that the type of "x" is a string. 

Компилятор не знает тип аргумента "x", и из-за этого не знает, является ли "Length" валидным методом. В большинстве случаев, это может быть исправлено через передачу "аннотации типа" компилятору F#, тогда он будет знать, какой тип необходимо использовать. В исправленной версии ниже, мы указываем, что тип "x" - string.

```fsharp
let stringLength (x:string) = x.Length         
```

> The parens around the `x:string` param are important. If they are missing, the compiler thinks that the return value is a string! That is, an "open" colon is used to indicate the type of the return value, as you can see in the example below.

Скобки вокруг параметра `x:string` важны. Если они будут пропущены, то компилятор решит, что строкой является возвращаемое значение! То есть, "открытое" двоеточие используется для обозначения типа возвращаемого значения, как видно в следующем примере.

```fsharp
let stringLengthAsInt (x:string) :int = x.Length         
```

> We're indicating that the x param is a string and the return value is an int. 

Мы указываем, что параметр `x` является строкой, а возвращаемым значением является целое число.

## Function types as parameters | Типы функций как параметры ##

> A function that takes other functions as parameters, or returns a function, is called a **higher-order function** (sometimes abbreviated as HOF). They are used as a way of abstracting out common behavior. These kinds of functions are extremely common in F#; most of the standard libraries use them. 

Функция которая принимает другие функции как параметры или возвращает функцию, называется **функцией высшего порядка** (**higher-order function** иногда сокращается до HOF). Их применяют в качестве абстракции от общего поведения. Данный вид функций очень распространен в F#, их использует большинство стандартных библиотек.

> Consider a function `evalWith5ThenAdd2`, which takes a function as a parameter, then evaluates the function with the value 5, and adds 2 to the result:

Рассмотрим функцию `evalWith5ThenAdd2`, которая принимает функцию в качестве параметра, затем вычисляет эту функцию от 5, и добавляет 2 к результату:

```fsharp
let evalWith5ThenAdd2 fn = fn 5 + 2     // same as fn(5) + 2
```

> The signature of this function looks like this:

Сигнатура данной функции выглядит так:

```fsharp
val evalWith5ThenAdd2 : (int -> int) -> int
```

> You can see that the domain is `(int->int)` and the range is `int`. What does that mean?  It means that the input parameter is not a simple value, but a function, and what's more is restricted only to functions that map `ints` to `ints`. The output is not a function, just an int.

Вы можете видеть, что domain равен `(int->int)`, а range `int`. Что это значит? Это значит, что входным параметром является не простое значение, а функция, более того, ограниченная функциями из `int` в `int`. Выходное же значение не функция, просто `int`.

> Let's try it:

> Попробуем:

```fsharp
let add1 x = x + 1      // define a function of type (int -> int)
evalWith5ThenAdd2 add1  // test it
```

> gives:

получим:

```fsharp
val add1 : int -> int
val it : int = 8
```

> "`add1`" is a function that maps ints to ints, as we can see from its signature. So it is a valid parameter for the `evalWith5ThenAdd2` function. And the result is 8. 

"`add1`" - это функция, которая сопоставляет `int` в `int`, как мы видим из сигнатуры. Она является допустимым параметром `evalWith5ThenAdd2`, и ее результат равен 8. 

> By the way, the special word "`it`" is used for the last thing that was evaluated; in this case the result we want. It's not a keyword, just a convention.

Кстати, специальное слово "`it`" используется для обозначения последнего вычисленного значения, в данном случае это результат, которого мы ждаил. Это не ключевое слово, это просто конвенция.

> Here's another one:

> Другой случай:

```fsharp
let times3 x = x * 3      // a function of type (int -> int)
evalWith5ThenAdd2 times3  // test it 
```

> gives:

дает:

```fsharp
val times3 : int -> int
val it : int = 17
```

> "`times3`" is also a function that maps ints to ints, as we can see from its signature. So it is also a valid parameter for the `evalWith5ThenAdd2` function. And the result is 17.

"`times3`" также является функцией, которая сопоставляет `int` в `int`, что видно из сигнатуры. Она также является валидным параметром для `evalWith5ThenAdd2`. Результат вычислений равен 17.

> Note that the input is sensitive to the types. If our input function uses `floats` rather than `ints`, it will not work. For example, if we have:

Следует учесть, что входные данные чувствительны к типам. Если передаваемая функция использует `float`, а не `int`, оно не сработает. Например, если у нас есть:

```fsharp
let times3float x = x * 3.0  // a function of type (float->float)  
evalWith5ThenAdd2 times3float 
```

> Evaluating this will give an error:

Попытка скомпилировать вернет ошибку:

```fsharp
error FS0001: Type mismatch. Expecting a int -> int but 
              given a float -> float    
```

> meaning that the input function should have been an `int->int` function.

сообщающую, что входная функция должна быть функцией `int->int`.

### Functions as output | Функции как выходные данные ###

> A function value can also be the output of a function. For example, the following function will generate an "adder" function that adds using the input value. 

Функции-значения также могут быть результатом функций. Например, следующая функция сгенерирует "adder" функцию, которая будет добавлять входное значение.

```fsharp
let adderGenerator numberToAdd = (+) numberToAdd
```

> The signature is:

Сигнатура:

```fsharp
val adderGenerator : int -> (int -> int)
```

> which means that the generator takes an `int`, and creates a function (the "adder") that maps `ints` to `ints`. Let's see how it works:

говорящая, что генератор принимает `int` и создает функцию ("adder"), которая сопоставляет `ints` в `ints`. Посмотрим как это работает:

```fsharp
let add1 = adderGenerator 1
let add2 = adderGenerator 2
```

> This creates two adder functions. The first generated function adds 1 to its input, and the second adds 2. Note that the signatures are just as we would expect them to be.

Создаются две функции сумматора. Первой создается функция добавляющая к вводу 1, вторая добавляет 2. Заметим, что сигнатуры именно такие, какие мы ожидали.

```fsharp
val add1 : (int -> int)
val add2 : (int -> int)
```

> And we can now use these generated functions in the normal way. They are indistinguishable from functions defined explicitly

Теперь можно использовать сгенерируемые функции как обычные, они неотличимы от функций определенных явно:

```fsharp
add1 5    // val it : int = 6
add2 5    // val it : int = 7
```

### Using type annotations to constrain function types | Использование аннотаций типа для ограничения типов функции ###

> In the first example, we had the function:

В первом примере была функция:

```fsharp
let evalWith5ThenAdd2 fn = fn 5 +2
    => val evalWith5ThenAdd2 : (int -> int) -> int
```

> In this case F# could deduce that "`fn`" mapped `ints` to `ints`, so its signature would be `int->int`

В данном примере F# может сделать вывод, что "`fn`" преобразует `int` в `int`, поэтому сигнатура будет `int->int`.

> But what is the signature of "fn" in this following case?

Но какова сигнатура "fn" в следующем случае?

```fsharp
let evalWith5 fn = fn 5
```

> Obviously, "`fn`" is some kind of function that takes an int, but what does it return? The compiler can't tell. If you do want to specify the type of the function, you can add a type annotation for function parameters in the same way as for a primitive type.

Понятно, что "`fn`" - разновидность функции, которая принимает `int`, но что она возвращает? Компилятор не может ответить на этот вопрос. В таких случаях, если возникает необходимость указать тип функции, можно добавить тип аннотации для параметров функций также как и для примитивных типов.

```fsharp
let evalWith5AsInt (fn:int->int) = fn 5
let evalWith5AsFloat (fn:int->float) = fn 5
```

> Alternatively, you could also specify the return type instead.

Кроме того, можно определить возвращаемый тип.

```fsharp
let evalWith5AsString fn :string = fn 5
```

> Because the main function returns a string, the "`fn`" function is also constrained to return a string, so no explicit typing is required for "fn". 

Т.к. основная функция возвращает `string`, функция "`fn`" также вынуждена возвращать `string`, таким образом не требуется явно типизировать "`fn`".

<a name="unit-type"></a>
## The "unit" type | Тип "unit" ##

> When programming, we sometimes want a function to do something without returning a value. Consider the function "`printInt`", defined below. The function doesn't actually return anything. It just prints a string to the console as a side effect.

В процессе программирования мы иногда хотим, чтобы функция делала что-то не возвращая ничего. Рассмотрим функцию "`printInt`". Функция действительно ничего не возвращает. Она просто выводит строку на консоль как побочный эффект.

```fsharp
let printInt x = printf "x is %i" x        // print to console
```

> So what is the signature for this function? 

Какова ее сигнатура?

```fsharp
val printInt : int -> unit
```

> What is this "`unit`"?

Что такое "`unit`"?  

> Well, even if a function returns no output, it still needs a range. There are no "void" functions in mathematics-land. Every function must have some output, because a function is a mapping, and a mapping has to have something to map to!

Даже если функция не возвращает значений, она все еще нуждается в range. В мире математики не существует "void" функций. Каждая функция должна что-то возвращать, потому-что функция - это отображение, а отображение должно что-то отображать!
 
![](../assets/img/Functions_Unit.png)
 
> So in F#, functions like this return a special range called "`unit`". This range has exactly one value in it, called "`()`". You can think of `unit` and `()` as somewhat like "void" (the type) and "null" (the value) in C#. But unlike void/null, `unit` is a real type and `()` is a real value. To see this, evaluate:

И так, в F# функции подобные этой возвращают специальный range называемый "`unit`". Данный range содержит только одно значение, называемое "`()`". Можно подумать, что `unit` и `()` что-то вроде "void" и "null" из C# соответственно. Но в отличии от них, `unit` является реальным типом, а `()` реальным значением. Чтобы убедиться в этом, достаточно выполнить:

```fsharp
let whatIsThis = ()
```

> and you will see the signature:

> будет получена следующая сигнатуру:

```fsharp
val whatIsThis : unit = ()
```

> Which means that the value "`whatIsThis`" is of type `unit` and has been bound to the value `()`

Которая указывает, что метка "`whatIsThis`" принадлежит типу `unit` и было связано со значением `()`.

> So, going back to the signature of "`printInt`", we can now understand it:

Теперь вернувшись к сигнатуре "`printInt`", можно понять значение это записи:

```fsharp
val printInt : int -> unit
```

> This signature says: `printInt` has a domain of `int` which it maps onto nothing that we care about.

Данная сигнатура говорит, что `printInt` имеет domain из `int`, который который преобразуется в нечто, что нас не интересует.

<a name="parameterless-functions"></a>

### Parameterless functions | Функции без параметров

> Now that we understand unit, can we predict its appearance in other contexts?  For example, let's try to create a reusable "hello world" function. Since there is no input and no output, we would expect it to have a signature `unit -> unit`. Let's see:

Теперь, когда мы понимаем `unit`, можем ли мы предсказать его появление в другом контексте? Например, попробуем создать многократно используемую функцию "hello world". Поскольку нет ни ввода, ни вывода, мы можем ожидать, сигнатуру `unit -> unit`. Посмотрим:

```fsharp
let printHello = printf "hello world"        // print to console
```

> The result is:

Результат:

```fsharp
hello world
val printHello : unit = ()
```

> Not quite what we expected. "Hello world" is printed immediately and the result is not a function, but a simple value of type unit. As we saw earlier, we can tell that this is a simple value because it has a signature of the form:  

_Не совсем то_, что мы ожидали. "Hello world" было выведено немедленно, а результатом стала не функция, а простое значение типа unit. Как мы видели ранее, мы можем сказать, что это простое значение, поскольку оно имеет сигнатуру вида:

```fsharp
val aName: type = constant
```

> So in this case, we see that `printHello` is actually a *simple value* with the value `()`. It's not a function that we can call again.

В данном примере мы видим, что `printHello` действительно является _простым значением_ `()`. Это не функция, которую мы можем вызвать позже.

> Why the difference between `printInt` and `printHello`?  In the `printInt` case, the value could not be determined until we knew the value of the x parameter, so the definition was of a function. In the `printHello` case, there were no parameters, so the right hand side could be determined immediately. Which it was, returning the `()` value, with the side effect of printing to the console. 

В чем разница между `printInt` и `printHello`? В случае `printInt`, значение не может быть определено, пока мы не узнаем значения параметра `x`, поэтому определение было функцией. В случае `printHello`, нет параметров, поэтому правая часть может быть определена на месте. И она была равна `()`, с side-эффектом в виде вывода на консоль.

> We can create a true reusable function that is parameterless by forcing the definition to have a unit argument, like this:

Можно создать настоящую многократно используемую функцию без параметров, заставляя определение иметь `unit` аргумент:

```fsharp
let printHelloFn () = printf "hello world"    // print to console
```

> The signature is now:

Ее сигнатура равна:

```fsharp
val printHelloFn : unit -> unit
```

> and to call it, we have to pass the `()` value as a parameter, like so:

и чтобы вызвать ее, мы должны передать `()` в качестве параметра:

```fsharp
printHelloFn ()
```

### Forcing unit types with the ignore function | _Усиление unit типов с помощью функции ignore _###

> In some cases the compiler requires a unit type and will complain. For example, both of the following will be compiler errors:

В некоторых случаях компилятор требует `unit` тип и будет жаловаться. Для примера, оба следующих случая вызовут ошибку компилятора:

```fsharp
do 1+1     // => FS0020: This expression should have type 'unit'

let something = 
  2+2      // => FS0020: This expression should have type 'unit'
  "hello"
```

> To help in these situations, there is a special function `ignore` that takes anything and returns the unit type. The correct version of this code would be:

Чтобы помочь в данных ситуациях, существует специальная функция `ignore`, которая принимает что угодно и возвращает `unit`. Правильная версия данного кода могла бы быть такой:

```fsharp
do (1+1 |> ignore)  // ok

let something = 
  2+2 |> ignore     // ok
  "hello"
```

## Generic types | Обобщенные типы ##

> In many cases, the type of the function parameter can be any type, so we need a way to indicate this. F# uses the .NET generic type system for this situation. 

В большинстве случаев, если тип параметра функции может быть любым типом, нам надо как-то сказать об этом. F# использует обобщения (generic) из .NET для таких ситуаций.

> For example, the following function converts the parameter to a string and appends some text:

Например, следующая функция конвертирует параметр в строку добавляя немного текста:

```fsharp
let onAStick x = x.ToString() + " on a stick"
```

> It doesn't matter what type the parameter is, as all objects understand `ToString()`. 

Не важно какого типа параметр, все объекты умеют в `ToString()`.

> The signature is:

Сигнатура:

```fsharp
val onAStick : 'a -> string
```

> What is this type called `'a`?  That is F#'s way of indicating a generic type that is not known at compile time. The apostrophe in front of the "a" means that the type is generic. The signature for the C# equivalent of this would be:

Что за тип `'a`? В F# это способ индикации обобщенного типа, который неизвестен на момент компиляции. Апостроф перед "a" означает, что тип является обобщенным. Эквивалент данной сигнатуры на C#:

```csharp
string onAStick<a>();   

//or more idiomatically 
string OnAStick<TObject>();   // F#'s use of 'a is like 
                              // C#'s "TObject" convention 
```

> Note that the F# function is still strongly typed with a generic type. It does *not* take a parameter of type `Object`. This strong typing is desirable so that when functions are composed together, type safety is still maintained.

Надо понимать, что данная F# функция все еще обладает строгой типизацией даже с обобщенными типами. Она *не* принимает параметр типа `Object`. Строгая типизация желаема, ибо позволяет при композиции функций вместе сохранять типобезопасность.

> Here's the same function being used with an int, a float and a string

Одна и та же функция используется для `int`, `float` и `string`.

```fsharp
onAStick 22
onAStick 3.14159
onAStick "hello"
```

> If there are two generic parameters, the compiler will give them different names: `'a` for the first generic, `'b` for the second generic, and so on. Here's an example:

Если есть два обобщенных параметра, то компилятор даст им два различных имени: `'a` для первого, `'b` для второго и т.д. Например:

```fsharp
let concatString x y = x.ToString() + y.ToString()
```

> The type signature for this has two generics: `'a` and `'b`:

В данной сигнатуре будет два обобщенных типа: `'a` и `'b`:

```fsharp
val concatString : 'a -> 'b -> string
```

> On the other hand, the compiler will recognize when only one generic type is required. In the following example, the x and y parameters must be of the same type:

С другой стороны, компилятор распознает, когда требуется только один универсальный тип. В следующем примере `x` и `y` должны быть одного типа:

```fsharp
let isEqual x y = (x=y)
```

> So the function signature has the same generic type for both of them:

Так сигнатура функции имеет одинаковый обобщенный тим для обоих параметров:

```fsharp
val isEqual : 'a -> 'a -> bool 
```

> Generic parameters are also very important when it comes to lists and more abstract structures, and we will be seeing them a lot in upcoming examples.

Обобщенные параметры также очень важны, когда дело касается списков и других абстрактных структур, и мы будем видеть их много в последующих примерах.

## Other types | Другие типы ##

> The types discussed so far are just the basic types. These types can be combined in various ways to make much more complex types. A full discussion of these types will have to wait for [another series](../series/understanding-fsharp-types.md), but meanwhile, here is a brief introduction to them so that you can recognize them in function signatures.

До сих пор обсуждались только базовые типы. Данные типы могут быть скомбинированы различными способами в более сложные типы. Полный их разбор будет позднее в [другой серии](../series/understanding-fsharp-types.md), но между тем, здесь кратко их разберем, так что бы можно было распознать их в сигнатуре функции.

> * **The "tuple" types**. These are pairs, triples, etc., of other types. For example `("hello", 1)` is a tuple made from a string and an int. The comma is the distinguishing characteristic of a tuple -- if you see a comma in F#, it is almost certainly part of a tuple!

* **Кортежи (tuples)**. Это пара, тройка и т.д. составленная из других типов. Например, `("hello", 1)` - кортеж сделанный на основе `string` и `int`. Запятая - отличительный признак кортежей, если в F# где-то замечена запятая, это почти гарантировано часть кортежа.

> In function signatures, tuples are written as the "multiplication" of the two types involved. So in this case, the tuple would have type:

В сигнатурах функций кортежи пишутся как "произведения" двух вовлеченных типов. В данном случае, кортеж будет иметь тип:

```fsharp
string * int      // ("hello", 1)
```

> * **The collection types**. The most common of these are lists, sequences, and arrays. Lists and arrays are fixed size, while sequences are potentially infinite (behind the scenes, sequences are the same as `IEnumerable`). In function signatures, they have their own keywords: "`list`", "`seq`", and "`[]`" for arrays.

* **Коллекции**. Наиболее распространенные из них - list (список), seq (перечисление) и массив. Списки и массивы имеют фиксированный размер, в то время как последовательности потенциально бесконечны (за кулисами последовательности - это те же самые `IEnumrable`). В сигнатурах функций они имеют свои собственные ключевые слова: "`list`", "`seq`" и "`[]`" для массивов.

```fsharp
int list          // List type  e.g. [1;2;3]
string list       // List type  e.g. ["a";"b";"c"]
seq<int>          // Seq type   e.g. seq{1..10}
int []            // Array type e.g. [|1;2;3|]
```

> * **The option type**. This is a simple wrapper for objects that might be missing. There are two cases: `Some` and `None`. In function signatures, they have their own "`option`" keyword:

* **Option (опциональный тип)**. Это простая обертка над объектами, которые могут отсутствовать. Существует два варианта: `Some` и `None`. В сигнатурах функций они имеют свое собственное ключевое слово "`option`":

```fsharp
int option        // Some(1)
```

> * **The discriminated union type**. These are built from a set of choices of other types. We saw some examples of this in the ["why use F#?"](../series/why-use-fsharp.md) series. In function signatures, they are referred to by the name of the type, so there is no special keyword.
> * **The record type**. These are like structures or database rows, a list of named slots. We saw some examples of this in the ["why use F#?"](../series/why-use-fsharp.md) series as well. In function signatures, they are referred to by the name of the type, so again there is no special keyword.

* **Размеченное объединение (discriminated union)**. Они построены из множества вариантов других типов. Мы видели некоторые примеры в ["why use F#?"](../series/why-use-fsharp.md). В сигнатурах функций на них ссылаются по имени типа, они не имеют специального ключевого слова.
* **Record тип (записи)**. Подобные структурам или строкам баз данных, списки именованных слотов. Мы также видели несколько примеров в ["why use F#?"](../series/why-use-fsharp.md). В сигнатурах функций они называются по имени типа, они также не имеют своего ключевого слова.

## Test your understanding of types | Проверьте свое понимание типов ##

> How well do you understand the types yet?  Here are some expressions for you -- see if you can guess their signatures. To see if you are correct, just run them in the interactive window!

Здесь представлено несколько выражений для проверки своего понимания сигнатур функций. Для проверки достаточно запустить их в интерактивном окне!

```fsharp
let testA   = float 2
let testB x = float 2
let testC x = float 2 + x
let testD x = x.ToString().Length
let testE (x:float) = x.ToString().Length
let testF x = printfn "%s" x
let testG x = printfn "%f" x
let testH   = 2 * 2 |> ignore
let testI x = 2 * 2 |> ignore
let testJ (x:int) = 2 * 2 |> ignore
let testK   = "hello"
let testL() = "hello"
let testM x = x=x
let testN x = x 1          // hint: what kind of thing is x?
let testO x:string = x 1   // hint: what does :string modify? 
```