---
layout: post
title: "Currying"
description: "Breaking multi-parameter functions into smaller one-parameter functions"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 5
categories: [Currying]
---

> After that little digression on basic types, we can turn back to functions again, and in particular the puzzle we mentioned earlier: if a mathematical function can only have one parameter, then how is it possible that an F# function can have more than one?

После небольшого экскурса в базовые типы мы можем снова вернуться к функциям, в частности, к ранее упомянутой загадке: если математическая функция может принимать только один параметр, то как в F# может существовать функция, принимающая большее число параметров?

> The answer is quite simple: a function with multiple parameters is rewritten as a series of new functions, each with only one parameter. And this is done automatically by the compiler for you. It is called "**currying**", after Haskell Curry, a mathematician who was an important influence on the development of functional programming.

Ответ довольно прост: функция с несколькими параметрами переписывается как серия новых функций, каждая из которых принимает только один параметр. Эту операцию компилятор выполняет автоматически, и называется она "**каррирование**" (_currying_), в честь Хаскела Карри, математика, который существенно повлиял на разработку функционального программирования.

> To see how this works in practice, let's use a very basic example that prints two numbers: 

Чтобы увидеть, как это работает на практике, воспользуемся простейшим примером кода, печатающим два числа:

```fsharp
//normal version
let printTwoParameters x y =
   printfn "x=%i y=%i" x y
```

> Internally, the compiler rewrites it as something more like:

На самом деле компилятор переписывает его приблизительно в такой форме:

```fsharp
//explicitly curried version
let printTwoParameters x  =    // only one parameter!
   let subFunction y =
      printfn "x=%i y=%i" x y  // new function with one param
   subFunction                 // return the subfunction
```

> Let's examine this in more detail:

Рассмотрим этот процесс подробнее:

> 1. Construct the function called "`printTwoParameters`" but with only *one* parameter: "x"
> 2. Inside that, construct a subfunction that has only *one* parameter: "y". Note that this inner function uses the "x" parameter but x is not passed to it explicitly as a parameter. The "x" parameter is in scope, so the inner function can see it and use it without needing it to be passed in. 
> 3. Finally, return the newly created subfunction.
> 4. This returned function is then later used against "y".  The "x" parameter is baked into it, so the returned function only needs the y param to finish off the function logic.

1. Объявляется функция с названием "`printTwoParameters`", но принимающая только _один_ параметр: "x".
2. Внутри неё создаётся локальная функция, которая также принимает только _один_ параметр: "y". Заметим, что локальная функция использует параметр "x", но x не передается в нее как аргумент. "x" находится в такой области видимости, что вложенная функция может видеть его и использовать без необходимости в его передаче.
3. Наконец, возвращается только что созданная локальная функция.
4. Возвращенная функция затем применяется к аргументу "y". Параметр "x" замыкается в ней, так что возвращаемая функция нуждается только в параметре y чтобы завершить свою логику.

> By rewriting it this way, the compiler has ensured that every function has only one parameter, as required. So when you use "`printTwoParameters`", you might think that you are using a two parameter function, but it is actually only a one parameter function!  You can see for yourself by passing only one argument instead of two:

Переписывая функции таким образом, комплиятор гарантирует, что каждая функция принимает только один параметр, как и требовалось. Таким образом, используя "`printTwoParameters`", можно подумать, что это функция с двумя параметрами, но на самом деле используется функция с только одним параметром. В этом можно убедиться, передав ей лишь один аргумент вместо двух:

```fsharp
// eval with one argument
printTwoParameters 1

// get back a function!
val it : (int -> unit) = <fun:printTwoParameters@286-3>
```

> If you evaluate it with one argument, you don't get an error, you get back a function.

Если вычислить ее с одним аргументом, мы не получим ошибку, а будет возвращена  функция.

> So what you are really doing when you call `printTwoParameters` with two arguments is:

Итак, вот что на самом деле происходит, когда `printTwoParameters` вызывается с двумя аргументами:

> * You call `printTwoParameters` with the first argument (x)
> * `printTwoParameters` returns a new function that has "x" baked into it.
> * You then call the new function with the second argument (y)

* Вызывается `printTwoParameters` с первым аргуметом (x)
* `printTwoParameters` возвращает новую функцию, в которой замкнут "x".
* Затем вызывается новая функция со вторым аргуметом (y)

> Here is an example of the step by step version, and then the normal version again.

Вот пример пошаговой и обыкновенной версий:

```fsharp
// step by step version
let x = 6
let y = 99
let intermediateFn = printTwoParameters x  // return fn with
                                           // x "baked in"
let result  = intermediateFn y

// inline version of above
let result  = (printTwoParameters x) y

// normal version
let result  = printTwoParameters x y
```

> Here is another example:

Вот другой пример:

```fsharp
//normal version
let addTwoParameters x y =
   x + y

//explicitly curried version
let addTwoParameters x  =      // only one parameter!
   let subFunction y =
      x + y                    // new function with one param
   subFunction                 // return the subfunction

// now use it step by step
let x = 6
let y = 99
let intermediateFn = addTwoParameters x  // return fn with
                                         // x "baked in"
let result  = intermediateFn y

// normal version
let result  = addTwoParameters x y
```

> Again, the "two parameter function" is actually a one parameter function that returns an intermediate function.

Опять же, "функция с двумя параметрами" на самом деле является функцией с одним параметром, которая возвращает промежуточную функцию.

> But wait a minute -- what about the "`+`" operation itself? It's a binary operation that must take two parameters, surely? No, it is curried like every other function. There is a function called "`+`" that takes one parameter and returns a new intermediate function, exactly like `addTwoParameters` above.

Но подождите, а что с операторором "`+`"? Это ведь бинарная операция, которая должна принимать два параметра? Нет, она тоже каррируется, как и другие функции. Это функция с именем "`+`", которая принимает один параметр и возвращает новую промежуточную функцию, в точности как `addTwoParameters` выше.

> When we write the statement `x+y`, the compiler reorders the code to remove the infix and turns it into `(+) x y`, which is the function named `+` called with two parameters.  Note that the function named "+" needs to have parentheses around it to indicate that it is being used as a normal function name rather than as an infix operator.

Когда мы пишем выражение `x+y`, компилятор _переупорядочивает_ код таким образом, чтобы преобразовать инфикс в `(+) x y`, _что является функцией с именем `+`, принимающей два параметра._ Заметим, что функция "+" нуждается в скобках, чтобы указать на то, она используется как обычная функция, а не как инфиксный оператор.

> Finally, the two parameter function named `+` is treated as any other two parameter function would be.

Наконец, функция с двумя параметрами называемая `+` обрабатывается как любая другая функция с двумя параметрами.

```fsharp
// using plus as a single value function
let x = 6
let y = 99
let intermediateFn = (+) x     // return add with x baked in
let result  = intermediateFn y

// using plus as a function with two parameters
let result  = (+) x y

// normal version of plus as infix operator
let result  = x + y
```

> And yes, this works for all other operators and built in functions like printf.

И да, это работает на все другие операторы и встроенные функции, такие как `printf`.

```fsharp
// normal version of multiply
let result  = 3 * 5

// multiply as a one parameter function
let intermediateFn = (*) 3   // return multiply with "3" baked in
let result  = intermediateFn 5

// normal version of printfn
let result  = printfn "x=%i y=%i" 3 5

// printfn as a one parameter function
let intermediateFn = printfn "x=%i y=%i" 3  // "3" is baked in
let result  = intermediateFn 5
```

## Signatures of curried functions | Сигнатуры каррированных функций ##

> Now that we know how curried functions work, what should we expect their signatures to look like?

Теперь, когда мы знаем, как работают каррированные функции, что можно ожидать от их сигнатур?

> Going back to the first example, "`printTwoParameters`", we saw that it took one argument and returned an intermediate function. The intermediate function also took one argument and returned nothing (that is, unit). So the intermediate function has type `int->unit`. In other words, the domain of `printTwoParameters` is `int` and the range is `int->unit`. Putting this together we see that the final signature is:

Возращаясь к первому примеру, "`printTwoParameter`", мы видели, что функция принимала один рагумент и возвращала промежуточную функцию. Промежуточная функция также принимала один аргумент и ничего не возврашала (т.е. `unit`). Поэтому промежуточная функция имела тип `int->unit`. Другими словами, domain `printTwoParameters` - это `int`, а range - `int->unit`. Собрав все это воедино мы увидим конечную сигнатуру:

```fsharp
val printTwoParameters : int -> (int -> unit)
```

> If you evaluate the explicitly curried implementation, you will see the parentheses in the signature, as written above, but if you evaluate the normal implementation, which is implicitly curried, the parentheses are left off, like so:

Если вычислить явно каррированную реализацию, можно увидеть скобки в сигнатуре, как написано выше, но если вычислить обыкновенную, неявно каррированную реализацию, скобок не будет:

```fsharp
val printTwoParameters : int -> int -> unit
```

> The parentheses are optional. If you are trying to make sense of function signatures it might be helpful to add them back in mentally.

Скобки необязательны. Но их можно представлять в уме, чтобы упростить восприятие сигнатур функций.

> At this point you might be wondering, what is the difference between a function that returns an intermediate function and a regular two parameter function?

Сейчас вам должно быть интересно, в чём разница между функцией, которая возвращает промежуточную функцию и обычной функцией с двумя параметрами.

> Here's a one parameter function that returns a function:

Вот функция с одним параметром, возвращающая другую функцию:

```fsharp
let add1Param x = (+) x
// signature is = int -> (int -> int)
```

> Here's a two  parameter function that returns a simple value:

А вот функция с двумя параметрами, которая возвращает простое значение:

```fsharp
let add2Params x y = (+) x y
// signature is = int -> int -> int
```

> The signatures are slightly different, but in practical terms, there *is* no difference*, only that the second function is automatically curried for you.

Их сигнатуры немного отличаются, но в практическом смысле между ними нет особой разницы, за исключением того факта, что вторая функция автоматически каррирована.

## Functions with more than two parameters | Функции с более чем двумя параметрами ##

> How does currying work for functions with more than two parameters? Exactly the same way: for each parameter except the last one, the function returns an intermediate function with the previous parameters baked in.

Как работает каррирование для функций с количеством параметров, большим двух? Точно так же: для каждого параметра, кроме последнего, функция возвращает промежуточную функцию, замыкающую предыдущий параметр.

> Consider this contrived example. I have explicitly specified the types of the parameters, but the function itself does nothing.

Рассмотрим этот затейливый пример. У меня явно объявлены типы параметров, но функция ничего не делает.

```fsharp
let multiParamFn (p1:int)(p2:bool)(p3:string)(p4:float)=
   ()   //do nothing

let intermediateFn1 = multiParamFn 42
   // intermediateFn1 takes a bool
   // and returns a new function (string -> float -> unit)
let intermediateFn2 = intermediateFn1 false
   // intermediateFn2 takes a string
   // and returns a new function (float -> unit)
let intermediateFn3 = intermediateFn2 "hello"
   // intermediateFn3 takes a float
   // and returns a simple value (unit)
let finalResult = intermediateFn3 3.141
```

> The signature of the overall function is:

Сигнатура всей функции:

```fsharp
val multiParamFn : int -> bool -> string -> float -> unit
```

> and the signatures of the intermediate functions are:

и сигнатуры промежуточных функций:

```fsharp
val intermediateFn1 : (bool -> string -> float -> unit)
val intermediateFn2 : (string -> float -> unit)
val intermediateFn3 : (float -> unit)
val finalResult : unit = ()
```

> A function signature can tell you how many parameters the function takes: just count the number of arrows outside of parentheses. If the function takes or returns other function parameters, there will be other arrows in parentheses, but these can be ignored. Here are some examples:

Сигнатура функции может сообщить о том, сколько параметров принимает функция: достаточно подсчитать число стрелок вне скобок. Если функция принимает или возвращает другую функцию, будут еще стрелки, но они будут в скобках и их можно будет проигнорировать. Вот некоторые примеры:

```fsharp
int->int->int      // two int parameters and returns an int

string->bool->int  // first param is a string, second is a bool,
                   // returns an int

int->string->bool->unit // three params (int,string,bool)
                        // returns nothing (unit)

(int->string)->int      // has only one parameter, a function
                        // value (from int to string)
                        // and returns a int

(int->string)->(int->bool) // takes a function (int to string)
                           // returns a function (int to bool)
```


## Issues with multiple parameters | Трудности с множественными параметрами ##

> The logic behind currying can produce some unexpected results until you understand it. Remember that you will not get an error if you evaluate a function with fewer arguments than it is expecting. Instead you will get back a partially applied function. If you then go on to use this partially applied function in a context where you expect a value, you will get obscure error messages from the compiler.

Пока вы не поймёте логику, которая стоит за каррированием, она будет приводить к некоторым неожиданным результатам. Помните, что вы не получите ошибку, если запустите функцию с меньшим количеством аргументов, чем ожидается. Вместо этого вы получите частично примененную функцию. Если затем вы воспользуетесь частично примененной функцией в контексте, где ожидается значение, можно получить малопонятную ошибку от компилятора.

> Here's an innocuous looking function:

Рассмотрим с виду безобидную функцию:

```fsharp
// create a function
let printHello() = printfn "hello"
```

> What would you expect to happen when we call it as shown below? Will it print "hello" to the console?  Try to guess before evaluating it, and here's a hint: be sure to take a look at the function signature.

Как думаете, что произойдет, если вызвать ее, как показано ниже? Выведится ли "hello" на консоль? Попробуйте догадаться до выполнения. Подсказка: посмотрите на сигнатуру функции.

```fsharp
// call it
printHello
```

> It will *not* be called as expected. The original function expects a unit argument that was not supplied, so you are getting a partially applied function (in this case with no arguments).

Вопреки ожиданиям вызова _не_ будет. Исходная функция ожидает `unit` как аргумент, который не был передан, поэтому была получена частично примененная функция (в данном случае без аргументов).

> How about this? Will it compile?

А что насчет этого случая? Будет ли он скомпилирован?

```fsharp
let addXY x y =
    printfn "x=%i y=%i" x
    x + y
```

> If you evaluate it, you will see that the compiler complains about the printfn line.

Если запустить его, компилятор пожалуется на строку с `printfn`.

```fsharp
printfn "x=%i y=%i" x
//^^^^^^^^^^^^^^^^^^^^^
//warning FS0193: This expression is a function value, i.e. is missing
//arguments. Its type is  ^a -> unit.
```

> If you didn't understand currying, this message would be very cryptic! All expressions that are evaluated standalone like this (i.e. not used as a return value or bound to something with "let") *must* evaluate to the unit value. And in this case, it is does *not* evaluate to the unit value, but instead evaluates to a function. This is a long winded way of saying that `printfn` is missing an argument.

Если нет понимания каррирования, данное сообщение может быть очень загадочным. Дело в том, что все выражения, которые вычисляются отдельно, как это (т.е. не используются как возвращаемое значение или привязка к чему-либо посредством "let") _должны_ вычисляться в `unit` значение. В данном случае, оно _не_ вычисляется в `unit` значение, но вместо этого возвращает функцию. Это длинный извилистный способ сказать, что `printfn` не хватает аргумента.

> A common case of errors like this is when interfacing with the .NET library. For example, the `ReadLine` method of a `TextReader` must take a unit parameter. It is often easy to forget this and leave off the parens, in which case you do not get a compiler error immediately, but only when you try to treat the result as a string.

В большинстве случаев ошибки, подобные этой, случаются при взаимодейтвии с библиотекой из мира .NET. Например, метод `Readline` класса `TextReader` должент принимать `unit` параметр. Об этом часто можно забыть, и не поставить скобки, в этом случае нельзя получить ошибку компилятора в момент "вызова", но она появится при попытке интерпретировать результат как строку.

```fsharp
let reader = new System.IO.StringReader("hello");

let line1 = reader.ReadLine        // wrong but compiler doesn't
                                   // complain
printfn "The line is %s" line1     //compiler error here!
// ==> error FS0001: This expression was expected to have
// type string but here has type unit -> string

let line2 = reader.ReadLine()      //correct
printfn "The line is %s" line2     //no compiler error
```

> In the code above, `line1` is just a pointer or delegate to the `Readline` method, not the string that we expected. The use of `()` in `reader.ReadLine()` actually executes the function.

В коде выше `line1` - просто указатель или делегат на метод  `Readline`, а не строка, как можно было бы ожидать. Использование `()` в `reader.ReadLine()` действительно вызовет функцию.

## Too many parameters | Слишком много параметров ##

> You can get similar cryptic messages when you have too many parameters as well. Here are some examples of passing too many parameters to printf.

Можно получить столь же загадочные сообщения, если передать функции слишком много параметров. Несколько примеров передачи слишком большого числа параметров в `printf`:

```fsharp
printfn "hello" 42
// ==> error FS0001: This expression was expected to have
//                   type 'a -> 'b but here has type unit

printfn "hello %i" 42 43
// ==> Error FS0001: Type mismatch. Expecting a 'a -> 'b -> 'c
//                   but given a 'a -> unit

printfn "hello %i %i" 42 43 44
// ==> Error FS0001: Type mismatch. Expecting a 'a->'b->'c->'d
//                   but given a 'a -> 'b -> unit
```

> For example, in the last case, the compiler is saying that it expects the format argument to have three parameters (the signature `'a -> 'b -> 'c -> 'd`  has three parameters) but it is given only two (the signature `'a -> 'b -> unit`  has two parameters).

Например, в последнем случае компилятор сообщает, что ожидается форматирующая строка с тремя параметрами (сигнатура `'a -> 'b -> 'c -> 'd` имеет три параметра), но вместо этого получена строка с двумя (у сигнатуры `'a -> 'b -> unit` два параметра)_.

> In cases not using `printf`, passing too many parameters will often mean that you end up with a simple value that you then try to pass a parameter to. The compiler will complain that the simple value is not a function.

В тех случаях, где не используется `printf`, передача большого количества параметров часто означает, что на определенном этапе вычислений было получено простое значение, которому пытаются передать параметр. Компилятор будет возмущаться, что простое значение не является функцией.

```fsharp
let add1 x = x + 1
let x = add1 2 3
// ==>   error FS0003: This value is not a function
//                     and cannot be applied
```

> If you break the call into a series of explicit intermediate functions, as we did earlier, you can see exactly what is going wrong.

Если разбить общий вызов на серию явных промежуточных функций, как делали ранее, можно увидеть, что именно происходит не так.

```fsharp
let add1 x = x + 1
let intermediateFn = add1 2   //returns a simple value
let x = intermediateFn 3      //intermediateFn is not a function!
// ==>   error FS0003: This value is not a function
//                     and cannot be applied
```