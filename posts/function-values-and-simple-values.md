---
layout: post
title: "Function Values and Simple Values"
description: "Binding not assignment"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 3
---

> Let's look at the simple function again

Еще раз рассмотрим эту простую функцию.

```fsharp
let add1 x = x + 1
```

> What does the "x" mean here? It means:

Что здесь означает "x"? Он означает:

> 1. Accept some value from the input domain.
> 2. Use the name "x" to represent that value so that we can refer to it later.

1. Принимает некоторое значение из domain (области определения).
2. Использует имя "x", чтобы представлять значение, к которому мы можем обратиться позже.

> This process of using a name to represent a value is called "binding". The name "x" is "bound" to the input value. 

Процесс использования имени для представления значения называется "привязкой" (binding). Имя "x" "привязано" к входному значению.

> So if we evaluate the function with the input 5 say, what is happening is that everywhere we see "x" in the original definition, we replace it with "5", sort of like search and replace in a word processor. 

Так что если вычислить функцию с вводом, скажем, равным 5, то произойдет следующее. Везде где стоит "x" в изначальном определении, ставится "5", аналогично функции "найти и заменить" в текстовом редакторе.

```fsharp
let add1 x = x + 1
add1 5
// replace "x" with "5"
// add1 5 = 5 + 1 = 6
// result is 6
```

> It is important to understand that this is not assignment. "x" is not a "slot" or variable that is assigned to the value and can be assigned to another value later on. It is a onetime association of the name "x" with the value. The value is one of the predefined integers, and cannot change. And so, once bound, x cannot change either; once associated with a value, always associated with a value. 

Важно понимать, что это не присваивание. "x" не "слот" и не переменная с присвоеным значением, которые можно изменить позднее. Это разовая ассоциация имени "x" с неким значением. Это иначение одно из предопределенных целых чисел, оно не может быть изменено. Т.е. однажды привязанный, x _не может быть измененен_. Метка единожды ассоциированная со значением, навсегда связана с этим значением.

> This concept is a critical part of thinking functionally: *there are no "variables", only values*.

Данный принцип - критически важная часть функционального мышления: *нет "переменных", только значения*.

## Function values | Функции как значения ##

> If you think about this a bit more, you will see that the name "`add1`" itself is just a binding to "the function that adds one to its input". The function itself is independent of the name it is bound to.

Если поразмыслить над этим чуть подольше, можно увидеть, что имя "`add`" само по себе - это просто привязка к "функция которая увеличивает ввод на еденицу.". Сама функция независима от имени которое к ней привязано.

> When you type `let add1 x = x + 1` you are telling the F# compiler "every time you see the name "`add1`", replace it with the function that adds 1 to its input". "`add1`" is called a **function value**.

Введя `let add1 x = x + 1`, мы говорим компилятору F# "каждый раз когда ты видешь имя "`add1`", замени его на функцию, которая добавляет 1 к вводу". "`add1`" называется **функцией-значением (function value)**.

> To see that the function is independent of its name, try:

Чтобы увидеть, что функция независит от ее имени, достаточно выполнить следующий код:

```fsharp
let add1 x = x + 1
let plus1 = add1
add1 5
plus1 5
```

> You can see that "`add1`" and "`plus1`" are two names that refer ("bound to") to the same function.

Как видно, "`add`'" и "`plus`" - это два имени ссылающихся (привязаныых к) одной и той же функции.

> You can always identify a function value because its signature has the standard form `domain -> range`. Here is a generic function value signature:

Идентифицировать функцию-значение всегда можно по ее сигнатуре, которая имеет страндартную форму `domain -> range`. Обобщенная сигнатура функции-значения:

```fsharp
val functionName : domain -> range
```

## Simple values | Простые значения ##

> Imagine an operation that always returned the integer 5 and didn't have any input. 

Представим операцию, которая ничего не принимает и всегда возвращает 5.

![](../assets/img/Functions_Const.png)
 
> This would be a "constant" operation.

Это была бы "костантная" операция.

> How would we write this in F#?  We want to tell the F# compiler "every time you see the name `c`, replace it with 5". Here's how:

Как можно было бы описать это в F#? Мы хотим сказать компилятору, "каждый раз, когда ты видишь имя `c`, замени его на 5. Вот так:

```fsharp
let c = 5
```

> which when evaluated, returns:

при вычислении вернется:

```fsharp
val c : int = 5
```

> There is no mapping arrow this time, just a single int. What's new is an equals sign with the actual value printed after it. The F# compiler knows that this binding has a known value which it will always return, namely the value 5. 

В этот раз нет стрелки сопоставления, всего лишь один int. Из нового - знак равенства с реальным значением выведенным после него. F# компилятор знает, что эта привязка имеет известное значение, которое будет возвращаться всегда, а именно число 5.

> In other words, we've just defined a constant, or in F# terms, a simple value. 

Другими словами, мы только что определили константу, или в терминах F#, простое значение.

> You can always tell a simple value from a function value because all simple values have a signature that looks like:

Dсегда можно отличить простое значение от функции-значения, потому все простые значения имеют подобную сигнатуру:

```fsharp
val aName: type = constant     // Note that there is no arrow
```

## Simple values vs. function values | Простые значение vs. функции-значения ##

> It is important to understand that in F#, unlike languages such as C#, there is very little difference between simple values and function values. They are both values which can be bound to names (using the same keyword `let`) and then passed around. And in fact, one of the key aspects of thinking functionally is exactly that: *functions are values that can be passed around as inputs to other functions*, as we will soon see.

Важно понять, что в F#, в отличии от других языков, таких как C#, существует очень небольшая разница между простыми значения и функциями-значениями. Оба типа являются значениями, которые могут быть связаны с именами (используя ключевое слово `let`), после чего они могут быть переданы везде. На самом деле, скоро мы увидим, идея о том, что *функции это значения которые могут быть переданы как входные данные другим функциям*, является одним из ключевых аспектов функционального мышления.

> Note that there is a subtle difference between a simple value and a function value. A function always has a domain and range and must be "applied" to an argument to get a result. A simple value does not need to be evaluated after being bound. Using the example above, if we wanted to define a "constant function" that returns five we would have to use 

Следует учесть, что существует небольшая разница между простым значением и функцией-значением. Функция всегда имеет domain и range, и должна быть "применена" к аргументу, чтобы вернуь результат. Простое значение не надо вычислять после привязки. Используя пример выше, если мы захотим определить "константную функцию", которая возвращает 5 мы могли бы использовать:

```fsharp
let c = fun()->5    
// or
let c() = 5
```

> The signature for these functions is:

Сигнатура таких функций выглядит так:

```fsharp
val c : unit -> int
```

> instead of:

А не так:

```fsharp
val c : int = 5
```

> More on unit, function syntax and anonymous functions later.

Больше информации о `unit`, синтаксисе функций и анонимных функциях будет сказано позднее.

## "Values" vs. "Objects" | "Значения" vs. "Объекты" ##

> In a functional programming language like F#, most things are called "values". In an object-oriented language like C#, most things are called "objects". So what is the difference between a "value" and an "object"?  

В функциональных языках программирования, таких как F#, большинство вещей называется "значениями". В объектно-ориентированных языках, таких как C#, большинство вещей называются "объектами". Какова разница между "значением" и "объектом"?

> A value, as we have seen above, is just a member of a domain. The domain of ints, the domain of strings, the domain of functions that map ints to strings, and so on. In principle, values are immutable. And values do not have any behavior attached them. 

Значение, как мы видели выше, является членом domain (домен). Домен целых чисел, домен строк, домен функций которые сопоставляют целым числам строки, и так далее. В принципе, значения иммутабельны (не изменяемы). И значения не имеют поведения прикрепленного к ним.

> An object, in a standard definition, is an encapsulation of a data structure with its associated behavior (methods). In general, objects are expected to have state (that is, be mutable), and all operations that change the internal state must be provided by the object itself (via "dot" notation).

Объекты, в каноническом определении, являются инкапсуляцией структуры данных с ассоциированным поведением (методами). В общем случае, объекты должны иметь состояние (то есть, быть изменяемыми), и все операции которые меняют внутреннее состояние должны предоставляться самим объектом (через "dot"-нотацию).

> In F#, even the primitive values have some object-like behavior. For example, you can dot into a string to get its length:

В F#, даже примитивные значения обладают некоторым объем "объектного" поведения. Напимер, можно через точку получить длину строки:

```fsharp
"abc".Length
```

> But, in general, we will avoid using "object" for standard values in F#, reserving it to refer to instances of true classes, or other values that expose member methods.

> Но в целом, мы будем избегать использования "объекта" для стандартны значений в F#, приберегая его для обращения к полноценным классам, или другим значениям, предоставляющим методы.

## Naming Values | Именование значений ##

> Standard naming rules are used for value and function names, basically, any alphanumeric string, including underscores.  There are a couple of extras:

Стандартные правила именования используемые для имен значений и функций, в основном, алфавитно-цифровая строка + символы подчеркивания. Но есть пара дополнений:

> You can put an apostrophe anywhere in a name, except the first character. So: 

Можно добавлять апостроф в любой части имени, исключая первый символ.

```fsharp
A'b'c     begin'  // valid names
```

> The final tick is often used to signal some sort of "variant" version of a value:

Последний случай часто используется как метка для "различных" версий какого-либо значения:

```fsharp
let f = x
let f' = derivative f
let f'' = derivative f'
```

> or define variants of existing keywords

или для переменных одноименных существующим ключевым словам

```fsharp
let if' b t f = if b then t else f
```

> You can also put double backticks around any string to make a valid identifier.

Можно также использовать двойные обратные ковычки для любой строки, чтобы сделать ее допустимым идентификатором.

```fsharp
``this is a name``  ``123``    //valid names
```

> You might want to use the double backtick trick sometimes:

Случаи, когда может понадобиться использовать трюк с двойными обратными ковычками:

> * When you want to  use an identifier that is the same as a keyword 

* Когда необходимо использовать идентификатор, который совпадает с ключевым словом

```fsharp
let ``begin`` = "begin"
```

> * When trying to use natural language for business rules, unit tests, or BDD style executable specifications a la Cucumber. 

Когда необходимо использовать естественные языки для бизнес правил, модульных тестов, или BBD стиля исполняемой документации типа Cucumber.

```fsharp
let ``is first time customer?`` = true
let ``add gift to order`` = ()
if ``is first time customer?`` then ``add gift to order``

// Unit test 
let [<Test>] ``When input is 2 then expect square is 4``=  
   // code here

// BDD clause
let [<Given>] ``I have (.*) N products in my cart`` (n:int) =  
   // code here
```

> Unlike C#, the naming convention for F# is that functions and values start with lowercase letters rather than uppercase (`camelCase` rather than `PascalCase`) unless designed for exposure to other .NET languages.  Types and modules use uppercase however.

В отличии от C#, конвенция именования F# требует, чтобы функции и значения начинались со строчной буквы, а не прописной (`camelCase`, а не `PascalCase`), кроме тех случаев, когда они предназначены для _взаимодействия с другими_ языками .NET. Однако типы и модули использут заглавные буквы (в начале).