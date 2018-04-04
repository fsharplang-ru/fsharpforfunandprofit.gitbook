---
layout: post
title: "Function signatures"
description: "A function signature can give you some idea of what it does"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 9
categories: [Functions]
---

> It may not be obvious, but F# actually has two syntaxes - one for normal (value) expressions, and one for type definitions. For example:

Это может быть неочевидным, но в F# существует два синтаксиса, один для нормальных выражений, другой для определения типов. Например:

```fsharp
[1;2;3]      // a normal expression
int list     // a type expression 

Some 1       // a normal expression
int option   // a type expression 

(1,"a")      // a normal expression
int * string // a type expression 
```

> Type expressions have a special syntax that is *different* from the syntax used in normal expressions. You have already seen many examples of this when you use the interactive session, because the type of each expression has been printed along with its evaluation. 

Выражения типов имеют специальный синтаксис, который *отличается* от синтаксиса используемого в обычных выражениях. Вы уже видели множество примеров, где использовалась интерактивная сессия, потому что тип каждого выражения выводился вместе с результатом выполнения.

> As you know, F# uses type inference to deduce types, so you don't often need to explicitly specify types in your code, especially for functions. But in order to work effectively in F#, you *do* need to understand the type syntax, so that you can build your own types, debug type errors, and understand function signatures. In this post, we'll focus on its use in function signatures.

Как вы знаете, F# использует вывод типов для их определения, поэтому вам зачастую не надо явно прописывать типы в вашем коде специально для функций. Однако для эффективной работы в F#, необходимо понимать синтаксис типов, чтобы вы могли определять свои собственные типы, отлаживать ошибки вывода типов и понимать сигнатуры функций. В данном посте я сосредоточусь на использовании сигнатур функций.

> Here are some example function signatures using the type syntax:

Вот несколько примеров сигнатур функций использующих синтаксис типов:

```fsharp
// expression syntax          // type syntax
let add1 x = x + 1            // int -> int 
let add x y = x + y           // int -> int -> int
let print x = printf "%A" x   // 'a -> unit
System.Console.ReadLine       // unit -> string
List.sum                      // 'a list -> 'a
List.filter                   // ('a -> bool) -> 'a list -> 'a list
List.map                      // ('a -> 'b) -> 'a list -> 'b list
```

## Understanding functions through their signatures | Понимание функций через сигнатуры ##

> Just by examining a function's signature, you can often get some idea of what it does. Let's look at some examples and analyze them in turn.

Часто, даже просто изучив сигнатуру функции, можно получить некоторое представление, о том, что она делает. Рассмотрим несколько примеров и проанализируем их по очереди.

```fsharp
// function signature 1
int -> int -> int
```

> This function takes two `int` parameters and returns another, so presumably it is some sort of mathematical function such as addition, subtraction, multiplication, or exponentiation. 

Данная функция берет два `int` параметра и возвращает еще один `int`, скорее всего, это разновидность математических функций, таких как сложение, вычитание, умножение или возведение в степень.

```fsharp
// function signature 2
int -> unit
```

> This function takes an `int` and returns a `unit`, which means that the function is doing something important as a side-effect. Since there is no useful return value, the side effect is probably something to do with writing to IO, such as logging, writing to a file or database, or something similar. 

Данная функция принимает `int` и возвращает `unit`, что означает, что функция делает что-то важное в виде side-эффекта. Т.к. она не возвращает полезного значения, side-эффект скорее всего производит операции записи в IO, такие как логирование, запись в базу данных или что-нибудь похожее.

```fsharp
// function signature 3
unit -> string
```

> This function takes no input but returns a `string`, which means that the function is conjuring up a string out of thin air! Since there is no explicit input, the function probably has something to do with reading (from a file say) or generating (a random string, say). 

Эта функция ничего не принимает, но возвращает `string`, что может означать, что функция получает строку из воздуха! Поскольку нет явного ввода, функция вероятно делает что-то с чтением (скажем из файла) или генерацией (н-р. случайная строка).

```fsharp
// function signature 4
int -> (unit -> string)
```

> This function takes an `int` input and returns a function that when called, returns strings. Again, the function probably has something to do with reading or generating. The input probably initializes the returned function somehow. For example, the input could be a file handle, and the returned function something like `readline()`. Or the input could be a seed for a random string generator. We can't tell exactly, but we can make some educated guesses.

Эта функция принимает `int` и возвращает функцию, которая при вызове возвращает строку. Опять же, вероятно функция производит операцию чтения или генерации. Ввод скорее всего каким-то образом инициализирует возвращаемую функцию. Например, ввод может быть идентификатором файла, а возвращаемая функция являться чем-то подобным `readline()`. Или ввод может быть конфигурацией генератора случайных строк. Мы не можем сказать точно, однако мы можем сделать некоторые обоснованные выводы.

```fsharp
// function signature 5
'a list -> 'a 
```

> This function takes a list of some type, but returns only one of that type, which means that the function is merging or choosing elements from the list. Examples of functions with this signature are `List.sum`, `List.max`, `List.head` and so on.

Функция принимает список некоторого типа, но возвращает лишь один элемент данного типа, что может означать, что функция аггрегирует список или выбирает один из его элементов. Подобную сигнатуру имеют `List.sum`, `List.max`, `List.head` и т.д.

```fsharp
// function signature 6
('a -> bool) -> 'a list -> 'a list 
```

> This function takes two parameters: the first is a function that maps something to a bool (a predicate), and the second is a list. The return value is a list of the same type. Predicates are used to determine whether a value meets some sort of criteria, so it looks like the function is choosing elements from the list based on whether the predicate is true or not and then returning a subset of the original list. A typical function with this signature is `List.filter`.

Эта функция принимает два параметра: первый - функция которая преобразует что-либо в `bool` (предикат), а второй - список. Возвращаемое значение является списком того же типа. Предикаты используют, чтобы определить, соответствует ли определенный объект какому-либо критерию, похоже, что данная функция выбирает элементы из списка на основе того, принимает ли предикат значение истинны или нет, после чего возвращает подмножество исходного списка. Типичной функцией с подобной сигнатурой является `List.filter`.

```fsharp
// function signature 7
('a -> 'b) -> 'a list -> 'b list
```

> This function takes two parameters: the first maps type `'a` to type `'b`, and the second is a list of `'a`. The return value is a list of a different type `'b`. A reasonable guess is that the function takes each of the `'a`s in the list, maps them to a `'b` using the function passed in as the first parameter, and returns the new list of `'b`s. And indeed, the prototypical function with this signature is `List.map`.

Функция принимает два параметра: первый - преобразует тип `'a` в тип `'b`, а второй - список типа `'a`. Возвращаемое значение является списком другого типа `'b`. Разумно будет предположить, что функция берет каждый элемент из списка `'a`, и преобразует их в `'b` используя функцию переданную в качестве первого параметра, после чего возвращает список `'b`. И действительно, `List.map` является прообразом функции с такой сигнатурой.

### Using function signatures to find a library method | Поиск библиотечных методов при помощи сигнатуры функции ###

> Function signatures are an important part of searching for library functions. The F# libraries have hundreds of functions in them and they can initially be overwhelming.  Unlike an object oriented language, you cannot simply "dot into" an object to find all the appropriate methods. However, if you know the signature of the function you are looking for, you can often narrow down the list of candidates quickly.

Сигнатуры функций важная часть поиска библиотечных функций. Библиотеки F# содержат сотни функций, что вначале может быть ошеломляющим. В отличие от объектно ориентированных языков, вы не можете просто "войти в объект" через точку, чтобы найти все связанные методы. Однако, если вы знаете сигнатуру функции, которую желаете найти, вы зачастую можете быстро сузить список кандидатов.

> For example, let's say you have two lists and you are looking for a function to combine them into one. What would the signature be for this function? It would take two list parameters and return a third, all of the same type, giving the signature:

Например, если скажем вы имеете два списка, и вы хотите найти функцию комбинирующую их в один. Какой сигнатурой обладала бы искомая функция? Она должна была бы принимать два списка в качестве параметров и возвращать третий, все одного типа:

```fsharp
'a list -> 'a list -> 'a list
```

> Now go to the [MSDN documentation for the F# List module](http://msdn.microsoft.com/en-us/library/ee353738), and scan down the list of functions, looking for something that matches.  As it happens, there is only one function with that signature:

Теперь перейдем на [сайт документации MSDN для модуля List](http://msdn.microsoft.com/en-us/library/ee353738), и поищем список функций, в поисках чего-либо похожего. Оказывается, что существует лишь одна функция с такой сигнатурой:

```fsharp
append : 'T list -> 'T list -> 'T list 
```

> which is exactly the one we want!

Это именно то, что нам нужно!

## Defining your own types for function signatures | Определение своего собственного типа для сигнатур функций ##

> Sometimes you may want to create your own types to match a desired function signature. You can do this using the "type" keyword, and define the type in the same way that a signature is written:

Иногда может понадобиться создать функцию своего собственного типа, чтобы соответствовать требуемой сигнатуре функции. Это можно сделать при помощи ключевого слова "type", аналогично примеру ниже:

```fsharp
type Adder = int -> int
type AdderGenerator = int -> Adder
```

> You can then use these types to constrain function values and parameters. 

Можно использовать данный тип, чтобы ограничить функции-значения и параметры.

> For example, the second definition below will fail because of type constraints. If you remove the type constraint (as in the third definition) there will not be any problem.

Например, второе определение ниже, упадет, т.к. тип ограничен. Если мы удалим ограничение типов (как в третьем определении) проблем не возникнет.

```fsharp
let a:AdderGenerator = fun x -> (fun y -> x + y)
let b:AdderGenerator = fun (x:float) -> (fun y -> x + y)
let c                = fun (x:float) -> (fun y -> x + y)
```

## Test your understanding of function signatures | Проверка понимания сигнатур функций ##

> How well do you understand function signatures?  See if you can create simple functions that have each of these signatures. Avoid using explicit type annotations! 

Как хорошо вы понимаете сигнатуры функций? Посмотрите, сможете ли вы создать простые функции, которые имеют подобные сигнатуры. Избегайте явных аннотаций типов!

```fsharp
val testA = int -> int
val testB = int -> int -> int
val testC = int -> (int -> int)      
val testD = (int -> int) -> int
val testE = int -> int -> int -> int
val testF = (int -> int) -> (int -> int)
val testG = int -> (int -> int) -> int
val testH = (int -> int -> int) -> int
```
