---
layout: post
title: "Mathematical functions"
description: "The impetus behind functional programming"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 2
---

> The impetus behind functional programming comes from mathematics. Mathematical functions have a number of very nice features that functional languages try to emulate in the real world. 

Функциональное программирование вдохновлено математикой. Математический функции имеют ряд очень приятных особенностей, которые функциональные языки пытаются претворить в жизнь.

> So first, let's start with a mathematical function that adds 1 to a number.

Итак, во-первых, начнем с математической функции, которая добавляет 1 к числу.

	Add1(x) = x+1

> What does this really mean?  Well it seems pretty straightforward. It means that there is an operation that starts with a number, and adds one to it. 

Что в действительности означает это выражение? Выглядит довольно просто. Оно означает, что существует такая операция, которая берет число и прибавляет к нему 1.

> Let's introduce some terminology:

Добавим немного терминологии:

> * The set of values that can be used as input to the function is called the *domain*. In this case, it could be the set of real numbers, but to make life simpler for now, let's restrict it to integers only.
> * The set of possible output values from the function is called the *range* (technically, the image on the codomain). In this case, it is also the set of integers.
> * The function is said to *map* the domain to the range.

* Множество допустимых входных значений функции называются _domain_ (область определения). В данном примере, это могло быть множество действительных чисел, но сделаем жизнь проще и ограничимся здесь только целыми числами.
* Множество возможных результатов функции (область значения) называется _range_ (технически, изображение **codomain-а**). В данном случае также множество целых.
* Функцией называют _преобразование_ (в оригинале _map_) из domain-а в range. (Т.е. из области определения в область значений.)

![](../assets/img/Functions_Add1.png)
 
> Here's how the definition would look in F#

Вот как это определение будет выглядеть на F#.

```fsharp
let add1 x = x + 1
```

> If you type that into the F# interactive window (don't forget the double semicolons) you will see the result (the "signature" of the function): 

Если ввести его в F# Interactive (не забудьте про двойные точку с запятой), то можно увидеть результат ("сигнатуру" функции):

```fsharp
val add1 : int -> int
```

> Let's look at that output in detail:

Рассмотрим вывод подробно:

> * The overall meaning is "the function `add1` maps integers (the domain) onto integers (the range)".
> * "`add1`" is defined as a "val", short for "value". Hmmm? what does that mean?  We'll discuss values shortly.
> * The arrow notation "`->`" is used to show the domain and range. In this case, the domain is the `int` type, and the range is also the `int` type.

* Общий смысл - это "функция `add1` сопоставляющая целые числа (из предметной области) с целыми (областью значений).
* "`add1`" определена как "val", сокращение от "value" (значения). Хм? что это значит? Мы обсудим значения чуть позже.
* Стрелочная нотация "->" используется, чтобы показать domain и range. В данном случае, domain является типом 'int', как и range.

> Also note that the type was not specified, yet the F# compiler guessed that the function was working with ints. (Can this be tweaked? Yes, as we'll see shortly).

Заметьте, что тип не был указан явно, но компилятор F# решил, что функция работает с int-ами. (Можно ли это изменить? Да, и скоро мы это увидим).

## Key properties of mathematical functions | Ключевые свойства математических функций ## 

> Mathematical functions have some properties that are very different from the kinds of functions you are used to in procedural programming.

Математические функции имеют ряд свойств, которые очень сильно отличают их от других типов функций, которые используются в процедурном программировании.

> * A function always gives the same output value for a given input value
> * A function has no side effects. 

* Функция всегда имеет один и тот же результат для данного входного значения.
* Функция не имеет побочных эффектов.

> These properties provide some very powerful benefits, and so functional programming languages try to enforce these properties in their design as well. Let's look at each of them in turn.

Эти свойства дают ряд заметных преимуществ, которые функциональные языки программирования пытаются по мере сил реализовать в своем дизайне. Рассмотрим каждое из них по очереди.

### Mathematical functions always give the same output for a given input | Математические функции всегда _возвращают одинаковый результат на заданное значение_ ###

> In imperative programming, we think that functions "do" something or "calculate" something. A mathematical function does not do any calculation -- it is purely a mapping from input to output. In fact, another way to think of defining a function is simply as the set of all the mappings. For example, in a very crude way we could define the "`add1`" function (in C#) as 

В императивном программировании мы думаем, что функции либо что-то "делают", либо что-то "подсчитывают". Математические функции ничего не считают, это чистые сопоставления из input в output. В самом деле, другое определение функции - это простое множество всех отображений. Например, очень грубо можно определить функцию "'add1'" (в C#) как

```csharp
int add1(int input)
{ 
   switch (input)
   {
   case 0: return 1;
   case 1: return 2;
   case 2: return 3;
   case 3: return 4;
   etc ad infinitum
   }
}
```

> Obviously, we can't have a case for every possible integer, but the principle is the same. You can see that absolutely no calculation is being done at all, just a lookup.

Очевидно, что невозможно иметь по case-у на каждое возможное число, но принцип тот же. При такой постановке никаких вычислений не производится, осуществляется лишь поиск.

### Mathematical functions are free from side effects | Математические функции свободны от побочных эффектов ###

> In a mathematical function, the input and the output are logically two different things, both of which are predefined. The function does not change the input or the output -- it just maps a pre-existing input value from the domain to a pre-existing output value in the range. 

В математической функции, входное и выходное значения логически две различные вещи, обе являющиеся предопределенными. Функция не изменяет входные или выходные данные и просто отображает предопределенное входное значение из области определения в предварительно определенное выходное значение в области значений.

> In other words, evaluating the function *cannot possibly have any effect on the input, or anything else for that matter*. Remember, evaluating the function is not actually calculating or manipulating anything; it is just a glorified lookup.

Другими словами, вычисление функции _не может иметь каких либо эффектов на входные данные или еще что-нибудь в подобном роде_". Следует запомнить, что вычисление функции в действительности не считает и не манипулирует чем-либо, это просто "прославленный" поиск.

> This "immutability" of the values is subtle but very important. If I am doing mathematics, I do not expect the numbers to change underneath me when I add them!  For example, if I have:

Эта "иммутабельность" значений очень тонка, но в тоже время очень важная вещь. Когда я занимаюсь математикой, я не жду, что числа будут изменяться в процессе их сложения. Например, если у меня:

	x = 5
	y = x+1

> I would not expect x to be changed by the adding of one to it. I would expect to get back a different number (y) and x would be left untouched. In the world of mathematics, the integers already exist as an unchangeable set, and the "add1" function simply defines a relationship between them.

Я не ожидаю, что `x` изменится при добавлении к нему 1. Я ожидаю, что получу другое число (`y`), и `x` должен остаться нетронутым. В мире математики целые числа уже существуют в неизменяемом множестве, и функция "add1" просто определяет отношения между ними.

### The power of pure functions | Сила чистых функций ###

> The kinds of functions which have repeatable results and no side effects are called "pure functions", and you can do some interesting things with them:

Те разновидности функций, что имеют повторяемые результаты и не имеют побочных эффектов называются "чистыми / pure функциями", и с ними можно сделать некоторые интересные вещи:

> * They are trivially parallelizable. I could take all the integers from 1 to 1000, say, and given 1000 different CPUs, I could get each CPU to execute the "`add1`" function for the corresponding integer at the same time, safe in the knowledge that there was no need for any interaction between them. No locks, mutexes, semaphores, etc., needed. 
> * I can use a function lazily, only evaluating it when I need the output. I can be sure that the answer will be the same whether I evaluate it now or later.
> * I only ever need to evaluate a function once for a certain input, and I can then cache the result, because I know that the same input always gives the same output.
> * If I have a number of pure functions, I can evaluate them in any order I like. Again, it can't make any difference to the final result.

* Из легко распараллелить. Скажем, можно бы взять целые числа в диапазоне от 1 до 1000 и раздать их 1000 различных процессоров, после чего поручить каждому CPU выполнить "'add1'" над соответствующим числом, одновременно будучи уверенным, что нет необходимости в каком-либо взаимодействии между ними. Не потребуется ни блокировок, ни мьютексов, ни семафоров, ни т.п.
* Можно использовать функции лениво, вычисляя их тогда, когда это необходимо для вывода. Можно быть уверенным, что ответ будет точно таким же, независимо от того, провожу вычисления сейчас или позже.
* Можно лишь один раз провести вычисления функции для конкретного ввода, после чего закешировать результат, потому-что известно, что данный ввод будет давать такой же вывод.
* Если есть множество чистых функций, их можно вычислять в любом порядке. Опять же, это не может повлиять на финальный результат.

> So you can see that if we can create pure functions in a programming language, we immediately gain a lot of powerful techniques. And indeed you can do all these things in F#:

Соответственно, если в языке программирования есть возможность создавать чистые функции, можно немедленно получить множество мощных приемов. И несомненно, все это можно сделать в F#:

> * You have already seen an example of parallelism in the ["why use F#?"](../series/why-use-fsharp.md) series. 
> * Evaluating functions lazily will be discussed in the ["optimization"](../series/optimization.md) series.
> * Caching the results of functions is called "memoization" and will also be discussed in the ["optimization"](../series/optimization.md) series.
> * Not caring about the order of evaluation makes concurrent programming much easier, and doesn't introduce bugs when functions are reordered or refactored. 

* Пример параллельных вычислений был в серии ["why use F#?"](../series/why-use-fsharp.md). 
* Ленивое вычисление функций будет обсуждено в серии ["optimization"](../series/optimization.md).
* Кэширование результатов функций называется мемоизацией и также будет обсуждено в серии ["optimization"](../series/optimization.md).
* Отсутствие необходимости в отслеживании порядка выполнения делает параллельное программирование гораздо проще и позволяет не сталкиваться с багами вызванными сменой порядка функций или рефакторинга.

## "Unhelpful" properties of mathematical functions | "Бесполезные" свойства математических функций ##

> Mathematical functions also have some properties that seem not to be very helpful when used in programming.

Математические функции также имеют некоторые свойства кажущиеся не очень полезными при программировании.

> * The input and output values are immutable
> * A function always has exactly one input and one output

* Входные и выходные значения неизменяемы
* Функции всегда имеют один ввод и один вывод

> These properties are mirrored in the design of functional programming languages too. Let's look at each of these in turn.

Данные свойства отражаются в дизайне функциональных языков программирования. Стоит рассмотреть их по отдельности.

> **The input and output values are immutable**

**Входные и выходные значения неизменяемы**

> Immutable values seem like a nice idea in theory, but how can you actually get any work done if you can't assign to variables in a traditional way?  

Иммутабельные значения в теории кажутся хорошей идеей, но как можно реально сделать какую-либо работу, если нет возможности назначить переменную традиционным способом.

> I can assure you that this is not as much as a problem as you might think. As you work through this series, you'll see how this works in practice.

Я могу заверить, что это не такая большая проблема как можно представить. Во ходе данной серией статей будет ясно, как это работает на практике.

> **Mathematical functions always have exactly one input and one output**

**Математические функции всегда имеют один ввод и один вывод** 

> As you can see from the diagrams, there is always exactly one input and one output for a mathematical function. This is true for functional programming languages as well, although it may not be obvious when you first use them. 

Как видно из диаграмм, для математической функции всегда существует только один ввод и только один вывод. Это также верно для функциональных языков программирования, хотя может быть неочевидным при первом использовании.

> That seems like a big annoyance. How can you do useful things without having functions with two (or more) parameters?

Это похоже на большое неудобство. Как можно сделать что-либо полезное без функций с двумя (или более) параметрами?

> Well, it turns there is a way to do it, and what's more, it is completely transparent to you in F#. It is called "currying" and it deserves its own post, which is coming up soon.

Оказывается, существует путь сделать это, и более того он является абсолютно прозрачным на F#. Называется он "каррированием", и заслуживает отдельный пост, который появится в ближайшее время.

> In fact, as you will later discover, these two "unhelpful" properties will turn out to be incredibly useful and a key part of what makes functional programming so powerful.

На самом деле, позже выяснится, что эти два "бесполезных" свойства станут невероятно ценными, и будут ключевой частью, которая делает функциональное программирование столь мощным.