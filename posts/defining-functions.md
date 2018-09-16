---
layout: post
title: "Defining functions"
description: "Lambdas and more"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 8
categories: [Functions, Combinators]
---


> We have seen how to create typical functions using the "let" syntax, below:

Мы видели как создавать обычные функции используя синтаксис "let", ниже:

```fsharp
let add x y = x + y
```

> In this section, we'll look at some other ways of creating functions, and tips for defining functions.

В этой статье мы рассмотрим некоторые другие способы создания функций, а также советы по их определению.

## Anonymous functions (a.k.a. lambdas) | Анонимные функции (лямбды) ##

> If you are familiar with lambdas in other languages, this will not be new to you. An anonymous function (or "lambda expression") is defined using the form:

Если вы знакомы с лямбдами в других языках, эта тема не станет новой. Анонимные функции (или "лямбда выражения") определяются посредством следующей формы:

```fsharp
fun parameter1 parameter2 etc -> expression
```

> If you are used to lambdas in C# there are a couple of differences:

По сравнению с лямбдами из C# есть два отличия:

> * the lambda must have the special keyword `fun`, which is not needed in the C# version
> * the arrow symbol is a single arrow `->` rather than the double arrow (`=>`) in C#.

* лямбды должны иметь специальное ключевое слово `fun`, которое отсутствует в C#
* используется одинарная стрелка `->`, вместо двойной `=>` из C#.

> Here is a lambda that defines addition:

Лямбда-определение функции сложения:

```fsharp
let add = fun x y -> x + y
```

> This is exactly the same as a more conventional function definition:

Та же функция в традиционной форме:

```fsharp
let add x y = x + y
```

> Lambdas are often used when you have a short expression and you don't want to define a function just for that expression. This is particularly common with list operations, as we have seen already.

Лямбды часто используются, когда есть короткое выражение и нет желания определять для него отдельную функцию. Что особенно характерно для операций со списком, как было показано ранее.

```fsharp
// with separately defined function
let add1 i = i + 1
[1..10] |> List.map add1

// inlined without separately defined function
[1..10] |> List.map (fun i -> i + 1)
```

> Note that you must use parentheses around the lambda.

Следует обратить внимание, что необходимо использовать скобки вокруг лямбд.

> Lambdas are also used when you want to make it clear that you are returning a function from another function. For example, the "`adderGenerator`" function that we talked about earlier could be rewritten with a lambda.

Лямбды также используются когда необходимо показать явно, что из функции возвращается другая функция. Например, "`adderGenerator`" который обсуждался ранее может быть переписан с помощью лямбды.

```fsharp
// original definition
let adderGenerator x = (+) x

// definition using lambda
let adderGenerator x = fun y -> x + y
```

> The lambda version is slightly longer, but makes it clear that an intermediate function is being returned.

Лямбда версия немного длиннее, но позволяет сразу понять, что будет возвращена промежуточная функция.

> You can nest lambdas as well. Here is yet another definition of `adderGenerator`, this time using lambdas only. 

Лямбды могут быть вложенными. Еще один пример определения `adderGenerator`, в этот раз чисто на лямбдах.

```fsharp
let adderGenerator = fun x -> (fun y -> x + y)
```

> Can you see that all three of the following definitions are the same thing?

Ясно ли вам, что все три следующих определения эквивалентны?

```fsharp
let adderGenerator1 x y = x + y 
let adderGenerator2 x   = fun y -> x + y
let adderGenerator3     = fun x -> (fun y -> x + y)
```

> If you can't see it, then do reread the [post on currying](../posts/currying.md). This is important stuff to understand!

Если нет, то перечитайте [главу о каррировании](../posts/currying.md). Это очень важно для понимания!

## Pattern matching on parameters | Сопоставление параметров с шаблоном ##

> When defining a function, you can pass an explicit parameter, as we have seen, but you can also pattern match directly in the parameter section. In other words, the parameter section can contain *patterns*, not just identifiers!

Когда определяется функция, ей можно передать параметры явно, как было показано ранее, но также можно произвести сопоставление с шаблоном прямо в секции параметров. Другими словами, секция параметров может содержать паттерны (шаблоны сопоставления), а не только идентификаторы!

> The following example demonstrates how to use patterns in a function definition:

Следующий пример демонстрирует как использовать шаблоны в определении функции:

```fsharp
type Name = {first:string; last:string} // define a new type
let bob = {first="bob"; last="smith"}   // define a value 

// single parameter style
let f1 name =                       // pass in single parameter   
   let {first=f; last=l} = name     // extract in body of function 
   printfn "first=%s; last=%s" f l

// match in the parameter itself
let f2 {first=f; last=l} =          // direct pattern matching 
   printfn "first=%s; last=%s" f l 

// test
f1 bob
f2 bob
```

> This kind of matching can only occur when the matching is always possible. For example, you cannot match on union types or lists this way, because some cases might not be matched.

Данный вид сопоставления может происходить только тогда, когда соответствие всегда разрешимо. Например, нельзя подобным способом матчить типы объединения и списки, потому-что некоторые случаи не могут быть сопоставлены.

```fsharp
let f3 (x::xs) =            // use pattern matching on a list
   printfn "first element is=%A" x
```

> You will get a warning about incomplete pattern matches.

Будет получено предупреждение о неполноте шаблона.

<a name="tuples"></a>

## A common mistake: tuples vs. multiple parameters | Распространенная ошибка: кортежи vs. множество параметров ##

> If you come from a C-like language, a tuple used as a single function parameter can look awfully like multiple parameters. They are not the same thing at all!   As I noted earlier, if you see a comma, it is probably part of a tuple. Parameters are separated by spaces.

Если вы пришли из C-подобного языка, кортежи использованные в качестве однопараметрической функции может выглядеть так же ужасно как и мультипараметрическая функция. Но это не одно и то же! Как я отметил ранее, если вы видите запятую, скорее всего это кортеж. Параметры же разделяются посредством запятой.

> Here is an example of the confusion:

Пример путаницы:

```fsharp
// a function that takes two distinct parameters
let addTwoParams x y = x + y

// a function that takes a single tuple parameter
let addTuple aTuple = 
   let (x,y) = aTuple
   x + y

// another function that takes a single tuple parameter 
// but looks like it takes two ints
let addConfusingTuple (x,y) = x + y
```

> * The first definition, "`addTwoParams`", takes two parameters, separated with spaces.
> * The second definition, "`addTuple`", takes a single parameter. It then binds "x" and "y" to the inside of the tuple and does the addition.
> * The third definition, "`addConfusingTuple`", takes a single parameter just like "`addTuple`", but the tricky thing is that the tuple is unpacked and bound as part of the parameter definition using pattern matching. Behind the scenes, it is exactly the same as "`addTuple`".

* Первое определение, "`addTwoParams`", принимает два параметра, разделенных пробелом.
* Второе определение, "`addTuple`", принимает один параметр. Этот параметр привязывает "x" и "y" из кортежа и суммирует их.
* Третье определение, "`addConfusingTuple`", принимает один параметр как и "`addTuple`", но трюк в том, что этот кортеж распаковывается и привязывается как часть определения параметра при помощи сопоставления с шаблоном. За кулисами все происходит точно так же как и в "`addTuple`".

> Let's look at the signatures (it is always a good idea to look at the signatures if you are unsure)

Посмотрите на сигнатуры (смотреть на сигнатуры - хорошая практика, если вы в чем то не уверены).

```fsharp
val addTwoParams : int -> int -> int        // two params
val addTuple : int * int -> int             // tuple->int
val addConfusingTuple : int * int -> int    // tuple->int
```

> Now let's use them:

А теперь сюда:

```fsharp
//test
addTwoParams 1 2      // ok - uses spaces to separate args
addTwoParams (1,2)    // error trying to pass a single tuple 
//   => error FS0001: This expression was expected to have type
//                    int but here has type 'a * 'b
```

> Here we can see an error occur in the second case above. 

Здесь мы видим ошибку во втором случае.

> First, the compiler treats `(1,2)` as a generic tuple of type `('a * 'b)`, which it attempts to pass as the first parameter to "`addTwoParams`".
Then it complains that the first parameter of `addTwoParams` is an `int`, and we're trying to pass a tuple.

Во-первых, компилятор трактует `(1,2)` как обобщенный параметр типа `('a * 'b)`, который пробует передать в качестве первого параметра в "`addTwoParams`". После чего жалуется, что первый параметр `addTwoParams` не является `int`, и была совершена попытка передачи кортежа.

> To make a tuple, use a comma!  Here's how to do it correctly:

Что бы сделать кортеж, используйте запятую! Т.е.:

```fsharp
addTuple (1,2)           // ok
addConfusingTuple (1,2)  // ok

let x = (1,2)                 
addTuple x               // ok

let y = 1,2              // it's the comma you need, 
                         // not the parentheses!      
addTuple y               // ok
addConfusingTuple y      // ok
```

> Conversely, if you attempt to pass multiple arguments to a function expecting a tuple, you will also get an obscure error.

И наоборот, если попытаться передать множество аргументов в функцию, ожидающую кортеж, можно также получить непонятную ошибку.

```fsharp
addConfusingTuple 1 2    // error trying to pass two args 
// => error FS0003: This value is not a function and 
//                  cannot be applied
```

> In this case, the compiler thinks that, since you are passing two arguments, `addConfusingTuple` must be curryable. So then "`addConfusingTuple 1`" would be a partial application that returns another intermediate function. Trying to apply that intermediate function with "2" gives an error, because there is no intermediate function! We saw this exact same error in the post on currying, when we discussed the issues that can occur from having too many parameters.

В этот раз, компилятор решил, что раз передаются два аргумента, `addConfusingTuple` должна быть каррируемой. Это значит, что "`addConfusingTuple 1`" является частичным применением и должно возвращать другую промежуточную функцию. Попытка вызвать промежуточную функцию на "2" выдает ошибку, т.к. здесь нет промежуточной функции! Мы видим ту же ошибку, что и в главе о каррировании, когда мы обсуждали проблемы связанные со слишком большим количеством параметров.

### Why not use tuples as parameters? | Почему бы не использовать кортежи в качестве параметров? ###

> The discussion of the issues with tuples above shows that there's another way to define functions with more than one parameter: rather than passing them in separately, all the parameters can be combined into a single composite data structure. In the example below, the function takes a single parameter, which is a tuple containing three items. 

Обсуждение кортежей выше показывает, что существует другой способ определения функций со множеством параметров: вместо передачи их по отдельности, все параметры могут быть собраны в виде одной структуры. В примере ниже, функция принимает один параметр, который является кортежем из трех элементов.

```fsharp
let f (x,y,z) = x + y * z
// type is int * int * int -> int

// test
f (1,2,3)
```

> Note that the function signature is different from a true three parameter function. There is only one arrow, so only one parameter, and the stars indicate that this is a tuple of `(int*int*int)`. 

Следует обратить внимание, что сигнатура отличается от сигнатуры функции с тремя параметрами. Здесь только одна стрелка, один параметр и звездочки, указывающие на кортеж `(int*int*int)`.

> When would we want to use tuple parameters instead of individual ones?  

В каких случаях лучше использовать кортеж параметров вместо отдельных значений?

> * When the tuples are meaningful in themselves. For example, if we are working with three dimensional coordinates, a three-tuple might well be more convenient than three separate dimensions.
> * Tuples are occasionally used to bundle data together in a single structure that should be kept together. For example, the `TryParse` functions in .NET library return the result and a Boolean as a tuple.  But if you have a lot of data that is kept together as a bundle, then you will probably want to define a record or class type to store it.

* Когда кортежи значимы сами по себе. Например, если производятся операции над трехмерными координатами, тройные кортежи могут быть более удобными чем три отдельных измерения.
* Кортежи иногда используются, чтобы объединить данные в единую структуру, которая должна сохраняться вместе. Например, `TryParse` методы из .NET библиотеки возвращают результат и булевую переменную в виде кортежа. Но если имеется достаточно большой объем данных передаваемых в связке, скорее всего он будет определен в виде записи или класса.

### A special case: tuples and .NET library functions | Особые случай: кортежи и функции .NET библиотеки ###

> One area where commas are seen a lot is when calling .NET library functions! 

Одна из областей, где запятые встречаются очень часто, это вызов функций .NET библиотеки!

> These all take tuple-like arguments, and so these calls look just the same as they would from C#:  

Они все принимают кортежи и эти вызовы выглядят также как на C#:

```fsharp
// correct
System.String.Compare("a","b")

// incorrect
System.String.Compare "a" "b"
```

> The reason is that .NET library functions are not curried and cannot be partially applied. *All* the parameters must *always* be passed in, and using a tuple-like approach is the obvious way to do this.

Причина этого кроется в том, что функции классического .NET не каррируемы и не могут быть частично применены. _Все_ параметры _всегда_ должны быть передаваться сразу, и использование кортеже-подобной передачи самый очевидный способ сделать это.

> But do note that although these calls look like tuples, they are actually a special case. Real tuples cannot be used, so the following code is invalid:

Однако следует заметить, что данные вызовы лишь выглядят как передачи кортежей, на самом деле это особый случай. В действительности кортежи не могут быть использованы, и следующий код невалиден:

```fsharp
let tuple = ("a","b")
System.String.Compare tuple   // error  

System.String.Compare "a","b" // error  
```

> If you do want to partially apply .NET library functions, it is normally trivial to write wrapper functions for them, as we have [seen earlier](../posts/partial-application.md), and as shown below:

Если есть желание частично применить функции .NET, это можно сделать тривиально при помощи обертки над ней, как делалось [ранее](../posts/partial-application.md), или как показано ниже:

```fsharp
// create a wrapper function
let strCompare x y = System.String.Compare(x,y)

// partially apply it
let strCompareWithB = strCompare "B"

// use it with a higher order function
["A";"B";"C"]
|> List.map strCompareWithB 
```


## Guidelines for separate vs. grouped parameters | Руководство по выбору отдельных и cгруппированных параметров ##

> The discussion on tuples leads us to a more general topic: when should function parameters be separate and when should they be grouped?

Обсуждение кортежей ведет к более общей теме: когда параметры должны быть отдельными, а когда cгруппированными?

> Note that F# is different from C# in this respect. In C# *all* the parameters are *always* provided, so the question does not even arise!  In F#, due to partial application, only some parameters might be provided, so you need to distinguish between those that are required to be grouped together vs. those that are independent. 

Следует обратить внимание на то, чем F# отличается от C# в этом отношении. В C# _все_ параметры _всегда_ переданы, поэтому данный вопрос там даже не возникает! В F# из-за частичного применения могут быть представлены лишь некоторые из параметров, поэтому необходимо проводить различие между случаем когда параметры должны быть объедененны и случаем, когда они независимы.

> Here are some general guidelines of how to structure parameters when you are designing your own functions.

Общие рекомендации о том, как структурировать параметры при проектировании собственных функций.

> * In general, it is always better to use separate parameters rather than passing them as a single structure such as a tuple or record. This allows for more flexible behavior such as partial application.
> * But, when a group of parameters *must* all be set at once, then *do* use some sort of grouping mechanism.  

* В общем случае, всегда лучше использовать раздельные параметры вместо передачи одной структуры будь то кортеж или запись. Это позволяет добиться более гибкого поведения, такого как частичное применение.
* Но, когда группа параметров _должна_ быть передана за раз, следует использовать какой-нибудь механизм группировки.

> In other words, when designing a function, ask yourself "could I provide this parameter in isolation?" If the answer is no, the parameters should be grouped.

Другими словами, когда разрабатываете функцию, спросите себя "Могу ли я предоставить это параметр отдельно?". Если ответ нет, то параметры должны быть сгруппированы.

> Let's look at some examples:

Рассмотрим несколько примеров:

```fsharp
// Pass in two numbers for addition. 
// The numbers are independent, so use two parameters
let add x y = x + y

// Pass in two numbers as a geographical co-ordinate. 
// The numbers are dependent, so group them into a tuple or record
let locateOnMap (xCoord,yCoord) = // do something

// Set first and last name for a customer.
// The values are dependent, so group them into a record.
type CustomerName = {First:string; Last:string}
let setCustomerName aCustomerName = // good
let setCustomerName first last = // not recommended

// Set first and last name and and pass the 
// authorizing credentials as well.
// The name and credentials are independent, keep them separate
let setCustomerName myCredentials aName = //good
```

> Finally, do be sure to order the parameters appropriately to assist with partial application (see the guidelines in the earlier [post](../posts/partial-application.md)). For example, in the last function above, why did I put the `myCredentials` parameter ahead of the `aName` parameter?

Наконец, убедитесь, что порядок параметров поможет в частичном применении (смотрите руководство [здесь](../posts/partial-application.md)). Например, в почему я поместил `myCredentials` перед `aName` в последней функции?

## Parameter-less functions | Функции без параметров ##

> Sometimes we may want functions that don't take any parameters at all. For example, we may want a "hello world" function that we can call repeatedly. As we saw in a previous section, the naive definition will not work.

Иногда может понадобиться функция, которая не принимает никаких параметров. Например, нужна функция "hello world" которую можно вызывать многократно. Как было показано в предыдущей секции, наивное определение не работает.

```fsharp
let sayHello = printfn "Hello World!"     // not what we want
```

> The fix is to add a unit parameter to the function, or use a lambda. 

Но это можно исправить, если добавить unit параметр к функции или использовать лямбду.

```fsharp
let sayHello() = printfn "Hello World!"           // good
let sayHello = fun () -> printfn "Hello World!"   // good
```

> And then the function must always be called with a unit argument:

После чего функция всегда должна вызываться с `unit` аргументом:

```fsharp
// call it
sayHello()
```

> This is particularly common with the .NET libraries. Some examples are:

Что происходит достаточно часто при взаимодействии с .NET библиотеками:

```fsharp
Console.ReadLine()
System.Environment.GetCommandLineArgs()
System.IO.Directory.GetCurrentDirectory()
```

> Do remember to call them with the unit parameter!

Запомните, вызывайте их с `unit` параметрами!

## Defining new operators | Определение новых операторов ##

> You can define functions named using one or more of the operator symbols (see the [F# documentation](http://msdn.microsoft.com/en-us/library/dd233204) for the exact list of symbols that you can use):

Можно определять функции с использованием одного и более операторных символа (смотрите [документацию](http://msdn.microsoft.com/en-us/library/dd233204) для ознакомления со списком символов):

```fsharp
// define
let (.*%) x y = x + y + 1
```

> You must use parentheses around the symbols when defining them. 

Необходимо использовать скобки вокруг символов для определения функции.

> Note that for custom operators that begin with `*`, a space is required; otherwise the `(*` is interpreted as the start of a comment: 

Операторы начинающиеся с `*` требуют пробел между скобкой и `*`, т.к. в F# `(*` выполняет роль начала комментария (как `/*...*/` в C#):

```fsharp
let ( *+* ) x y = x + y + 1
```

> Once defined, the new function can be used in the normal way, again with parens around the symbols:

Единожды определенная, новая функция может быть использована обычным способом, если будет завернута в скобки:

```fsharp
let result = (.*%) 2 3
```

> If the function has exactly two parameters, you can use it as an infix operator without parentheses.

Если функция используется с двумя параметрами, можно использовать инфиксную операторную запись без скобок.

```fsharp
let result = 2 .*% 3
```

> You can also define prefix operators that start with `!` or `~` (with some restrictions -- see the [F# documentation on operator overloading](http://msdn.microsoft.com/en-us/library/dd233204#prefix))

Можно также определять префиксные операторы начинающиеся с `!` или `~` (с некоторыми ограничениями, смотрите [документацию](http://msdn.microsoft.com/en-us/library/dd233204#prefix))

```fsharp
let (~%%) (s:string) = s.ToCharArray()

//use
let result = %% "hello"
```

> In F# it is quite common to create your own operators, and many libraries will export operators with names such as `>=>` and `<*>`.

В F# определение операторов достаточно частая операция, и многие библиотеки будут экспортировать операторы с именами типа `>=>`и `<*>`.

## Point-free style | Point-free стиль ##

> We have already seen many examples of leaving off the last parameter of functions to reduce clutter. This style is referred to as **point-free style** or **tacit programming**.

Мы уже видели множество примеров функций у которых отсутствовали последние параметры, чтобы снизить уровень хаоса. Этот стиль называется **point-free стилем** или **молчаливым программированием (tacit programming)**.

> Here are some examples:

Вот несколько примеров:

```fsharp
let add x y = x + y   // explicit
let add x = (+) x     // point free

let add1Times2 x = (x + 1) * 2    // explicit
let add1Times2 = (+) 1 >> (*) 2   // point free

let sum list = List.reduce (fun sum e -> sum+e) list // explicit
let sum = List.reduce (+)                            // point free
```

> There are pros and cons to this style. 

У данного стиля есть свои плюсы и минусы.

> On the plus side, it focuses attention on the high level function composition rather than the low level objects. For example "`(+) 1 >> (*) 2`" is clearly an addition operation followed by a multiplication. And "`List.reduce (+)`" makes it clear that the plus operation is key, without needing to know about the list it is actually applied to. 

Одним из плюсов является то, что акцент производится на композицию функций высшего порядка вместо возни с низкоуровневыми объектами. Например, "`(+) 1 >> (*) 2`" - явное сложение с последующим умножением. А "`List.reduce (+)`" дает понять, что важна операция сложения, безотносительно информации о списке.

> Point-free helps to clarify the underlying algorithm and reveal commonalities between code -- the "`reduce`" function used above is a good example of this -- it will be discussed in a planned series on list processing.

Бесточечный стиль позволяет сосредоточиться на базовом алгоритме и выявить общие черты в коде. "`reduce`" функция использованная выше является хорошим примером. Эта тема будет обсуждаться в запланированной серии по обработке списков.

> On the other hand, too much point-free style can make for confusing code. Explicit parameters can act as a form of documentation, and their names (such as "list") make it clear what the function is acting on. 

С другой стороны, чрезмерное использование подобного стиля может сделать код малопонятным. Явные параметры действуют как документация и их имена (такие как "list") облегчают понимание того, что делает функция.

> As with anything in programming, the best guideline is to use the approach that provides the most clarity.

Как и все в программировании, лучшая рекомендация, предпочитайте тот подход, что обеспечивает наибольшую ясность.

## Combinators | Комбинаторы ##

> The word "**combinator**" is used to describe functions whose result depends only on their parameters.  That means there is no dependency on the outside world, and in particular no other functions or global value can be accessed at all.

"**Комбинаторами**" называют функции, чей результат зависит только от их параметров. Это означает, что не существует зависимости от внешнего мира, и, в частности, никакие другие функции или глобальные значения не могут повлиять на них.

> In practice, this means that a combinator function is limited to combining its parameters in various ways.

На практике, это означает, что комбинаторные функции ограничены комбинацией их параметров различными способами.

> We have already seen some combinators already: the "pipe" operator and the "compose" operator.  If you look at their definitions, it is clear that all they do is reorder the parameters in various ways

Мы уже видели несколько комбинаторов: "pipe" и оператор композиции. Если посмотреть на их определения, то понятно, что все, что они делают, это переупорядочивают параметры различными способами.

```fsharp
let (|>) x f = f x             // forward pipe
let (<|) f x = f x             // reverse pipe
let (>>) f g x = g (f x)       // forward composition
let (<<) g f x = g (f x)       // reverse composition
```

> On the other hand, a function like "printf", although primitive, is not a combinator, because it has a dependency on the outside world (I/O).

С другой стороны, функции подобные "printf", хоть и примитивны, но не являются комбинаторами, потому-что имеют зависимость от внешнего мира (I/O).

### Combinator birds | ? Комбинаторные птички (TODO: Обсудить) ###

> Combinators are the basis of a whole branch of logic (naturally called "combinatory logic") that was invented many years before computers and programming languages. Combinatory logic has had a very large influence on functional programming.

Комбинаторы являются основой целого раздела логики (естественно называемого "комбинаторная логика"), который был изобретен за много лет до компьютеров и языков программирования. Комбинаторная логика имеет очень большое влияние на функциональное программирование.

> To read more about combinators and combinatory logic, I recommend the book "To Mock a Mockingbird" by Raymond Smullyan.  In it, he describes many other combinators and whimsically gives them names of birds.  Here are some examples of some standard combinators and their bird names:

Чтобы узнать больше о комбинаторах и комбинаторной логики, я рекомендую книгу "To Mock a Mockingbird" Raymond-а Smullyan-а. В ней он объясняет другие комбинаторы и причудливо дает им названия птиц. Вот несколько примеров стандартных комбинаторов и их птичьих имен:

```fsharp
let I x = x                // identity function, or the Idiot bird
let K x y = x              // the Kestrel
let M x = x >> x           // the Mockingbird
let T x y = y x            // the Thrush (this looks familiar!)
let Q x y z = y (x z)      // the Queer bird (also familiar!)
let S x y z = x z (y z)    // The Starling
// and the infamous...
let rec Y f x = f (Y f) x  // Y-combinator, or Sage bird
```

> The letter names are quite standard, so if you refer to "the K combinator", everyone will be familiar with that terminology.

Буквенные имена вполне стандартные, так что можно ссылаться на K-комбинатор всякому, кто знаком с данной терминологией.

> It turns out that many common programming patterns can be represented using these standard combinators. For example, the Kestrel is a common pattern in fluent interfaces where you do something but then return the original object. The Thrush is the pipe operation, the Queer bird is forward composition, and the Y-combinator is famously used to make functions recursive.

Получается, что множество распространенных шаблонов программирования могут быть представлены через данные стандартные комбинаторы. Например, Kestrel является обычным паттерном в fluent интерфейсе где вы делаете что-то, но возвращаете оригинальный объект. Thrush - пайп, Queer - прямая композиция, а Y-комбинатор отлично справляется с созданием рекурсивных функций.

> Indeed, there is a well-known theorem that states that any computable function whatsoever can be built from just two basic combinators, the Kestrel and the Starling. 

На самом деле, существует широко известная теорема, что любая вычислимая функция может быть построена при помощи лишь двух базовых комбинаторов, Kestrel-а и Starling-а.

### Combinator libraries | Библиотеки комбинаторов ###

> A combinator library is a code library that exports a set of combinator functions that are designed to work together. The user of the library can then easily combine simple functions together to make bigger and more complex functions, like building with Lego.  

Библиотеки комбинаторов - это библиотеки которые экспортируют множество комбинаторных функций, которые разработаны с учетом их совместного использования. Пользователь подобной библиотеки может легко комбинировать функции вместе, чтобы получить еще большие и сложные функции, как кубики легко.

> A well designed combinator library allows you to focus on the high level operations, and push the low level "noise" to the background. We've already seen some examples of this power in the examples in ["why use F#"](../series/why-use-fsharp.md) series, and the `List` module is full of them -- the "`fold`" and "`map`" functions are also combinators, if you think about it.

Хорошо спроектированная библиотека комбинаторов позволяет сосредоточиться на высокоуровневых функциях, и скрыть низкоуровневый "шум". Мы уже видели их силу в нескольких примерах в серии ["why use F#"](../series/why-use-fsharp.md), и модуль `List` полон таких функций, "`fold`" и "`map`" также являются комбинаторами, если вы подумали об этом.

> Another advantage of combinators is that they are the safest type of function. As they have no dependency on the outside world they cannot change if the global environment changes.  A function that reads a global value or uses a library function can break or alter between calls if the context is different. This can never happen with combinators. 

Другое преимущество комбинаторов - они являются самым безопасным типом функций. Т.к. они не имеют зависимостей от внешнего мира, они не могут изменяться, при изменении глобальной среды. Функция которая читает глобальное значение или использует библиотечные функции, может сломаться или измениться между вызовами если контекст изменится. Этого никогда не произойдет с комбинаторами.

> In F#, combinator libraries are available for parsing (the FParsec library), HTML construction, testing frameworks, and more.  We'll discuss and use combinators further in later series.

В F# библиотеки комбинаторов доступны для парсинга (FParsec), создания HTML, тестирующих фреймворков и т.д. Мы обсудим и воспользуемся комбинаторами позднее в следующих сериях.

## Recursive functions | Рекурсивные функции ##

> Often, a function will need to refer to itself in its body.  The classic example is the Fibonacci function:

Часто функции необходимо ссылаться на саму себя из ее тела. Классический пример - функция Фибоначчи.

```fsharp
let fib i = 
   match i with
   | 1 -> 1
   | 2 -> 1
   | n -> fib(n-1) + fib(n-2)
```

> Unfortunately, this will not compile: 

К сожалению, данная функция не сможет скомпилироваться:

	error FS0039: The value or constructor 'fib' is not defined

> You have to tell the compiler that this is a recursive function using the rec keyword.

Необходимо указать компилятору, что это рекурсивная функция используя ключевое слово `rec`.

```fsharp
let rec fib i = 
   match i with
   | 1 -> 1
   | 2 -> 1
   | n -> fib(n-1) + fib(n-2)
```

> Recursive functions and data structures are extremely common in functional programming, and I hope to devote a whole later series to this topic.

Рекурсивные функции и структуры данных очень распространены в функциональном программировании, и я надеюсь посвятить этой теме целую серию позднее.