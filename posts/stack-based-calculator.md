---
layout: post
title: "Worked example: A stack based calculator"
description: "Using combinators to build functionality"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 13
categories: [Combinators, Functions, Worked Examples]
---

> In this post, we'll implement a simple stack based calculator (also known as "reverse Polish" style). The implementation is almost entirely done with functions, with only one special type and no pattern matching at all, so it is a great testing ground for the concepts introduced in this series.

В этой статье мы реализуем простой стековый калькулятор (также известный как "обратная Польская нотация"). Реализация практически полностью построена на функциях, лишь с одним специальным типом, и вообще без сопоставления с образцом, так что это превосходный полигон для концепций, затронутых в нашей серии.

> If you are not familiar with a stack based calculator, it works as follows: numbers are pushed on a stack, and operations such as addition and multiplication pop numbers off the stack and push the result back on.

Если вы не знакомы с подобным калькулятором, то он работает следующим образом: числа помещаются в стек, а операции, такие как сложение и умножение, забирают числа с вершины стека, после чего помещают обратно полученный результат операции.

> Here is a diagram showing a simple calculation using a stack:

Схема простого вычисления на стеке:

![Stack based calculator diagram](../assets/img/stack-based-calculator.png) 

> The first steps to designing a system like this is to think about how it would be used. Following a Forth like syntax, we will give each action a label, so that the example above might want to be written something like:

Прежде чем проектировать подобную систему, следует порассуждать над тем, как она будет использоваться. Следуя Forth-подобному синтаксису, дадим каждому действию соответствующую метку, чтобы в приведенном выше примере можно было написать нечто вроде:

    EMPTY ONE THREE ADD TWO MUL SHOW

> We might not be able to get this exact syntax, but let's see how close we can get.

Возможно, точно такой синтаксис получить не удастся, но попробуем приблизиться к этому как можно ближе.

## The Stack data type | Тип данных стека

> First we need to define the data structure for a stack. To keep things simple, we'll just use a list of floats.

Во первых, нужно определить структуру данных для стека. Для простоты можно использовать список чисел с плавающей точкой.

```fsharp
type Stack = float list
```

> But, hold on, let's wrap it in a [single case union type](../posts/discriminated-unions.md#single-case) to make it more descriptive, like this:

Но лучше обернуть его в [single case union type](../posts/discriminated-unions.md#single-case), чтобы сделать тип более наглядным, например так:

```fsharp
type Stack = StackContents of float list
```

> For more details on why this is nicer, read the discussion of single case union types in [this post](../posts/discriminated-unions.md#single-case).

Почему лучше делать именно так, можно прочитать [здесь](../posts/discriminated-unions.md#single-case).

> Now, to create a new stack, we use `StackContents` as a constructor:

Теперь создадим новый стек, используя `StackContents` в качестве конструктора:

```fsharp
let newStack = StackContents [1.0;2.0;3.0]
```

> And to extract the contents of an existing Stack,  we pattern match with `StackContents`:

Для извлечения содержимого из существующего Stack-а используется сопоставление с образцом `StackContents`:

```fsharp
let (StackContents contents) = newStack 

// Значение "contents" будет равно
// float list = [1.0; 2.0; 3.0]
```


## The Push function | Функция Push

> Next we need a way to push numbers on to the stack. This will be simply be prepending the new value at the front of the list using the "`::`" operator.  

Далее нам потребуется способ помещать числа в этот стек. Для этого достаточно добавить новое значение в начало списка, используя "`::`".

> Here is our push function:

Пример функции:

```fsharp
let push x aStack =   
    let (StackContents contents) = aStack
    let newContents = x::contents
    StackContents newContents 
```

> This basic function has a number of things worth discussing.

Эта функция имеет ряд особенностей, которые стоит обсудить.

> First, note that the list structure is immutable, so the function must accept an existing stack and return a new stack.  It cannot just alter the existing stack. In fact, all of the functions in this example will have a similar format like this:

Во первых, следует обратить внимание на то, что структура `list` неизменяемая, значит функция должна принимать существующий стек и возвращать новый. Это не просто изменение существующего стека. По факту, все функции в данном примере будут иметь подобный формат:

>     Input: a Stack plus other parameters
>     Output: a new Stack

    Input: Stack и еще какой-либо параметр
    Output: новый Stack

> Next, what should the order of the parameters be? Should the stack parameter come first or last? If you remember the discussion of [designing functions for partial application](../posts/partial-application), you will remember that the most changeable thing should come last. You'll see shortly that this guideline will be born out.

Во вторых, почему параметры идут именно в таком порядке? Почему стек должен идти первым или последним? В разделе[проектирование функций с частичным применением](../posts/partial-application) говорилось, что наиболее часто меняющийся параметр должен идти последним. Вскоре можно будет убедиться, что эти рекомендации соблюдаются.

> Finally, the function can be made more concise by using pattern matching in the function parameter itself, rather than using a `let` in the body of the function.

Наконец, функцию можно сделать более краткой с помощью сопоставления с образцом в самом параметре функции, вместо `let` в теле функции.

> Here is the rewritten version:

Переписанная версия:

```fsharp
let push x (StackContents contents) =   
    StackContents (x::contents)
```

> Much nicer!

Намного лучше!

> And by the way, look at the nice signature it has:

Между прочим, посмотрите на её изящную сигнатуру:

```fsharp
val push : float -> Stack -> Stack
```

> As we know from a [previous post](../posts/function-signatures), the signature tells you a lot about the function.
> In this case, I could probably guess what it did from the signature alone, even without knowing that the name of the function was "push".
> This is one of the reasons why it is a good idea to have explicit type names. If the stack type had just been a list of floats, it wouldn't have been as self-documenting.

Как говорилось [ранее](../posts/function-signatures), сигнатура сообщает нам очень многое.
В данном случае я мог бы догадаться, что делает данная функция, лишь по ее сигнатуре, даже не зная, что она называется "push".
Это еще одна причина по которой было хорошей идеей иметь явные имена типа. Если бы стек был лишь списком чисел с плавающей точкой, то функция не была бы столь самодокументированной.

> Anyway, now let's test it:

Так или иначе, проверим:

```fsharp
let emptyStack = StackContents []
let stackWith1 = push 1.0 emptyStack 
let stackWith2 = push 2.0 stackWith1
```

> Works great!

Прекрасно работает!

## Building on top of "push" | Надстрока вершины стека при помощи "push"

> With this simple function in place, we can easily define an operation that pushes a particular number onto the stack. 

С помощью этой простой функции можно легко определить операцию, помещающую определенное число в стек.

```fsharp
let ONE stack = push 1.0 stack
let TWO stack = push 2.0 stack
```

> But wait a minute! Can you see that the `stack` parameter is used on both sides? In fact, we don't need to mention it at all. Instead we can skip the `stack` parameter and write the functions using partial application as follows:

Но подождите минуту! Вы же видите, что параметр `stack` упоминается с обеих сторон выражения? В действительно, совершенно необязательно упоминать его два раза. Вместо этого можно опустить параметр и написать функцию с частичным применением:

```fsharp
let ONE = push 1.0
let TWO = push 2.0
let THREE = push 3.0
let FOUR = push 4.0
let FIVE = push 5.0
```

> Now you can see that if the parameters for `push` were in a different order, we wouldn't have been able to do this. 

Теперь очевидно, что если бы функция `push`имела другой порядок параметров, `stack` пришлось бы упоминать дважды.

> While we're at it, let's define a function that creates an empty stack as well:

Стоит также определить функцию, создающую пустой стек: 

```fsharp
let EMPTY = StackContents []
```

> Let's test all of these now:

Проверим полученные функции:

```fsharp
let stackWith1 = ONE EMPTY 
let stackWith2 = TWO stackWith1
let stackWith3  = THREE stackWith2 
```

> These intermediate stacks are annoying ? can we get rid of them? Yes!  Note that these functions ONE, TWO, THREE all have the same signature:

Эти промежуточные стеки раздражают? Можно ли избавиться от них? Конечно! Заметьте, что функции ONE, TWO и THREE имеют одинаковую сигнатуру:

```fsharp
Stack -> Stack
```

> This means that they can be chained together nicely! The output of one can be fed into the input of the next, as shown below:

А значит, они прекрасно соединяются вместе! Вывод одной функции может быть подан на вход следующей:

```fsharp
let result123 = EMPTY |> ONE |> TWO |> THREE 
let result312 = EMPTY |> THREE |> ONE |> TWO
```


## Popping the stack | Выталкивание из стека // TODO: Исправить

> That takes care of pushing onto the stack ? what about a `pop` function next?

С добавлением в стек разобрались, но что насчет функции `pop`?

> When we pop the stack, we will return the top of the stack, obviously, but is that all?  

При извлечении из стека, очевидно, необходимо вернуть вершину стека, но только ли ее?

> In an object-oriented style, [the answer is yes](http://msdn.microsoft.com/en-us/library/system.collections.stack.pop.aspx). In an OO approach, we would *mutate* the stack itself behind the scenes, so that the top element was removed.

В объектно-ориентированном стиле [ответом будет "да"](http://msdn.microsoft.com/en-us/library/system.collections.stack.pop.aspx). Но в случае ООП, стек был бы изменен за кулисами, так что верхний элемент был бы удален.

> But in a functional style, the stack is immutable.  The only way to remove the top element is to create a *new stack* with the element removed.
> In order for the caller to have access to this new diminished stack, it needs to be returned along with the top element itself.

Однако в функциональном стиле стек неизменяем. Есть только один способ удалить верхний элемент - создать _новый стек_ без этого элемента. Для того, чтобы вызывающий объект имел доступ к новому уменьшенному стеку, его необходимо вернуть вместе с верхним элементом.

> In other words, the `pop` function will have to return *two* values, the top plus the new stack.  The easiest way to do this in F# is just to use a tuple.

Другими словами, функция `pop` должна возвращать _два_ значения, верхний элемент и новый стек. Простейший способ сделать это в F# - это просто использовать кортеж.

> Here's the implementation:

Реализация:

```fsharp
/// Вытолкнуть значение из стека
/// и вернуть его и новый стек в виде пары
let pop (StackContents contents) = 
    match contents with 
    | top::rest -> 
        let newStack = StackContents rest
        (top,newStack)
```

> This function is also very straightforward. 

Полученная функция также очень проста.

> As before, we are extracting the `contents` directly in the parameter.

Как и прежде, `contents` извлекается прямо из параметра.

> We then use a `match..with` expression to test the contents.

Затем с помощью выражения `match..with` проверяется содержимое `contents`.

> Next, we separate the top element from the rest, create a new stack from the remaining elements and finally return the pair as a tuple.

Потом верхний элемент отделяется от остальной части списка, создается новый стек на основе оставшихся элементов, и наконец все это возвращается парой в виде кортежа.

> Try the code above and see what happens. You will get a compiler error!
> The compiler has caught a case we have overlooked -- what happens if the stack is empty?

Попробуйте запустить этот код и посмотрите, что произойдёт. Вы получите ошибку компиляции!
Компилятор обнаружил случай, который не был отработан -- что произойдет, если стек пуст?

> So now we have to decide how to handle this. 

Надо решить, как это обрабатывать.

> * Option 1: Return a special "Success" or "Error" state, as we did in a [post from the "why use F#?" series](../posts/correctness-exhaustive-pattern-matching.md).
> * Option 2: Throw an exception.

* Вариант 1: Вернуть специальное состояние "Success" или "Error", как это делалось в [посте из серии "why use F#?"](../posts/correctness-exhaustive-pattern-matching.md).
* Вариант 2: Выбросить исключение.

> Generally, I prefer to use error cases, but in this case, we'll use an exception. So here's the `pop` code changed to handle the empty case:

Обычно я предпочитаю использовать специальное состояние для ошибки, но в данном конкретном случае я предпочел выбросить исключение. Исправленная версия `pop` с обработкой пустого случая:

```fsharp
let pop (StackContents contents) = 
    match contents with 
    | top::rest -> 
        let newStack = StackContents rest
        (top,newStack)
    | [] -> 
        failwith "Stack underflow"
```

> Now let's test it:

Проверим:

```fsharp
let initialStack = EMPTY  |> ONE |> TWO 
let popped1, poppedStack = pop initialStack
let popped2, poppedStack2 = pop poppedStack
```

> and to test the underflow:

и тест на исключение:

```fsharp
let _ = pop EMPTY
```

## Writing the math functions | Арифметические функции

> Now with both push and pop in place, we can work on the "add" and "multiply" functions:

Теперь когда добавление и удаление на месте, можно начать работу с функциями "add" и "multiply":

```fsharp
let ADD stack =
   let x,s = pop stack  //извлечь вершину стека
   let y,s2 = pop s     //извлечь вершину полученного стека
   let result = x + y   //вычислить арифметическое выражение
   push result s2       //добавить его обратно в стек

let MUL stack = 
   let x,s = pop stack  //извлечь вершину стека
   let y,s2 = pop s     //извлечь вершину полученного стека
   let result = x * y   //вычислить арифметическое выражение 
   push result s2       //добавить его обратно в стек
```

> Test these interactively:

Проверка в интерактивном режиме:

```fsharp
let add1and2 = EMPTY |> ONE |> TWO |> ADD
let add2and3 = EMPTY |> TWO |> THREE |> ADD
let mult2and3 = EMPTY |> TWO |> THREE |> MUL
```

> It works!

Работает!

### Time to refactor... | Время рефакторинга

> It is obvious that there is significant duplicate code between these two functions. How can we refactor?

Очевидно, что в этих двух функциях значительное количество кода дублируется. Как мы можем это исправить?

> Both functions pop two values from the stack, apply some sort of binary function, and then push the result back on the stack.  This leads us to refactor out the common code into a "binary" function that takes a two parameter math function as a parameter:

Обе функции извлекают два значения из стека, применяют к ним некую бинарную функцию, после чего помещают результат обратно в стек. Можно вывести общий код в функцию "binary", которая принимает математическую функцию с двумя параметрами:

```fsharp
let binary mathFn stack = 
    // вытолкнуть вершину стека
    let y,stack' = pop stack    
    // снова вытолкнуть вершину стека
    let x,stack'' = pop stack'  
    // вычислить
    let z = mathFn x y
    // положить результат обратно в стек
    push z stack''      
```

> *Note that in this implementation, I've switched to using ticks to represent changed states of the "same" object, rather than numeric suffixes. Numeric suffixes can easily get quite confusing.*

_Обратите внимание, что в этой реализации разные версии "одного" объекта помечены различным количеством штрихов-кавычек. Это сделано потому, что числовые суффиксы могут легко привести к путанице._

> Question: why are the parameters in the order they are, instead of `mathFn` being after `stack`?

Вопрос: почему параметры имеют именно такой порядок, вместо `mathFn` идущего после `stack`?

> Now that we have `binary`, we can define ADD and friends more simply:

Теперь, когда есть функция `binary`, можно гораздо проще определить ADD и другие функции:

> Here's a first attempt at ADD using the new `binary` helper:

Первая попытка реализации ADD с помощью `binary`:

```fsharp
let ADD aStack = binary (fun x y -> x + y) aStack 
```

> But we can eliminate the lambda, as it is *exactly* the definition of the built-in `+` function!  Which gives us:

Но можно избавиться от лямбды, т.к. она представляет *точное* определение встроенной функции `+`:

```fsharp
let ADD aStack = binary (+) aStack 
```

> And again, we can use partial application to hide the stack parameter. Here's the final definition:

Опять же, можно использовать частичное применение, чтобы скрыть параметр "стек". Итоговое определение:

```fsharp
let ADD = binary (+)
```

> And here's the definition of some other math functions:

Определение других математических функций:

```fsharp
let SUB = binary (-)
let MUL = binary (*)
let DIV = binary (../)
```

> Let's test interactively again.

Попробуйте в интерактивном режиме:

```fsharp
let div2by3 = EMPTY |> THREE|> TWO |> DIV
let sub2from5 = EMPTY  |> TWO |> FIVE |> SUB
let add1and2thenSub3 = EMPTY |> ONE |> TWO |> ADD |> THREE |> SUB
```

> In a similar fashion, we can create a helper function for unary functions

Схожим образом можно создать вспомогательную функцию для унарных операций

```fsharp
let unary f stack = 
    let x,stack' = pop stack
    push (f x) stack'
```
    
> And then define some unary functions:

И определить несколько унарных функций:

```fsharp
let NEG = unary (fun x -> -x)
let SQUARE = unary (fun x -> x * x)
```

> Test interactively again:

Интерактивный режим:

```fsharp
let neg3 = EMPTY  |> THREE|> NEG
let square2 = EMPTY  |> TWO |> SQUARE
```

## Putting it all together | Собираем всё вместе

> In the original requirements, we mentioned that we wanted to be able to show the results, so let's define a SHOW function.

В изначальных требованиях упоминалось, что мы хотели бы иметь возможность показать результаты, поэтому стоит определить функцию SHOW.

```fsharp
let SHOW stack = 
    let x,_ = pop stack
    printfn "The answer is %f" x
    stack  // продолжаем работать с тем же стеком
```

> Note that in this case, we pop the original stack but ignore the diminished version. The final result of the function is the original stack, as if it had never been popped.

Обратите внимание, что в данном случае новая версия стека, полученная через `pop`, игнорируется. Конечным же результатом объявляется исходный стек, как будто он никогда не изменялся.

> So now finally, we can write the code example from the original requirements

Наконец, можно написать следующий пример из оригинальных требований

```fsharp
EMPTY |> ONE |> THREE |> ADD |> TWO |> MUL |> SHOW
```

### Going further | Идём дальше

> This is fun -- what else can we do?

Это весело, но что еще можно сделать?

> Well, we can define a few more core helper functions:

Можно определить несколько дополнительных функций:

```fsharp
/// Дублирование значения на вершине стека
let DUP stack = 
    // получение вершины стека
    let x,_ = pop stack  
    // добавление её обратно в стек
    push x stack 
    
/// Обменять местами два верхних значения
let SWAP stack = 
    let x,s = pop stack  
    let y,s' = pop s
    push y (push x s')   
    
/// Создать начальную точку
let START  = EMPTY
```
    
> And with these additional functions in place, we can write some nice examples:

С помощью этих дополнительных функций можно написать несколько изящных примеров:

```fsharp
START
    |> ONE |> TWO |> SHOW

START
    |> ONE |> TWO |> ADD |> SHOW 
    |> THREE |> ADD |> SHOW 

START
    |> THREE |> DUP |> DUP |> MUL |> MUL // 27

START
    |> ONE |> TWO |> ADD |> SHOW  // 3
    |> THREE |> MUL |> SHOW       // 9
    |> TWO |> SWAP |> DIV |> SHOW // 9 div 2 = 4.5
```

## Using composition instead of piping | Использование композиции вместо конвейеризации

> But that's not all. In fact, there is another very interesting way to think about these functions. 

Но и это ещё не всё. На самом деле, существует другой интересный способ представления этих функций.

> As I pointed out earlier, they all have an identical signature: 

Как отмечалось ранее, все они имеют одинаковую сигнатуру:

```fsharp
Stack -> Stack
```

> So, because the input and output types are the same, these functions can be composed using the composition operator `>>`, not just chained together with pipes. 

Поскольку ввод и вывод имеют одинаковые типы, эти функции могут быть скомпонованы еще и при помощи оператора композиции `>>`, а не только посредством конвейерных операторов.

> Here are some examples:

Несколько примеров:

```fsharp
// определение новой функции
let ONE_TWO_ADD = 
    ONE >> TWO >> ADD 

START |> ONE_TWO_ADD |> SHOW

// определяем ещё одну
let SQUARE = 
    DUP >> MUL 

START |> TWO |> SQUARE |> SHOW

// и ещё одна функция
let CUBE = 
    DUP >> DUP >> MUL >> MUL 

START |> THREE |> CUBE |> SHOW

// и ещё
let SUM_NUMBERS_UPTO = 
    DUP                     // n  
    >> ONE >> ADD           // n+1
    >> MUL                  // n(n+1)
    >> TWO >> SWAP >> DIV   // n(n+1) / 2 

START |> THREE |> SQUARE |> SUM_NUMBERS_UPTO |> SHOW
```

> In each of these cases, a new function is defined by composing other functions together to make a new one. This is a good example of the "combinator" approach to building up functionality.

В каждом из этих примеров новая функция определяется с помощью композиции других функций. Это хороший пример "комбинаторного" подхода к построению функциональности.

## Pipes vs composition | Конвейеры против композиции

> We have now seen two different ways that this stack based model can be used; by piping or by composition. So what is the difference? And why would we prefer one way over another?

Мы видели два различных способа использования нашей модели; при помощи конвейеров и композиции. Но в чем разница? И почему надо предпочесть один из способов другому?

> The difference is that piping is, in a sense, a "realtime transformation" operation. When you use piping you are actually doing the operations right now, passing a particular stack around.

Разница заключается в том, что конвейеры в некотором смысле являются операцией "трансформации в реальном времени". В момент использования конвейера операции выполняются сразу, через передачу определенного стека.

> On the other hand, composition is a kind of "plan" for what you want to do, building an overall function from a set of parts, but *not* actually running it yet.

С другой стороны, композиция - это нечто вроде "плана", который мы хотим осуществить, построение функций из набора составляющих без непосредственного применения.

> So for example, I can create a "plan" for how to square a number by combining smaller operations:

Например, можно создать "план" вычисления квадрата числа через комбинацию малых операций:

```fsharp
let COMPOSED_SQUARE = DUP >> MUL 
```

> I cannot do the equivalent with the piping approach.

Я не могу привести эквивалент на основе конвейеров.

```fsharp
let PIPED_SQUARE = DUP |> MUL 
```

> This causes a compilation error. I have to have some sort of concrete stack instance to make it work:

Это приведет к ошибке компиляции. Мне нужен какой-то конкретный экземпляр стека, чтобы выражение заработало:

```fsharp
let stackWith2 = EMPTY |> TWO
let twoSquared = stackWith2 |> DUP |> MUL 
```

> And even then, I only get the answer for this particular input, not a plan for all possible inputs, as in the COMPOSED_SQUARE example.

И даже в этом случае, я смогу получить ответ только для этого конкретного ввода, а не обобщенный план вычисления на основе любого ввода, как в примере с `COMPOSED_SQUARE`.

> The other way to create a "plan" is to explicitly pass in a lambda to a more primitive function, as we saw near the beginning:

Другой способ создать "план" - явно передать лямбду в более примитивные функции:

```fsharp
let LAMBDA_SQUARE = unary (fun x -> x * x)
```

> This is much more explicit (and is likely to be faster) but loses all the benefits and clarity of the composition approach.

Это более явный способ (и скорее всего более быстрый), но теряются все преимущества и ясность композиционного подхода.

> So, in general, go for the composition approach if you can!

В общем, если это возможно, следует стремиться к композиционному подходу!

## The complete code | Полный код

> Here's the complete code for all the examples so far.

Полный код для всех примеров, представленных выше:

```fsharp
// ==============================================
// Типы
// ==============================================

type Stack = StackContents of float list

// ==============================================
// Стековые примитивы
// ==============================================

/// Поместить значение в стек
let push x (StackContents contents) =   
    StackContents (x::contents)

/// Вытолкнуть значение из стека и вернуть его
/// и новый стек в виде пары
let pop (StackContents contents) = 
    match contents with 
    | top::rest -> 
        let newStack = StackContents rest
        (top,newStack)
    | [] -> 
        failwith "Stack underflow"

// ==============================================
// Ядро (операторы)
// ==============================================

// вытолкнуть два верхних элемента
// применить к ним бинарную операцию
// положить результат в стек 
let binary mathFn stack = 
    let y,stack' = pop stack    
    let x,stack'' = pop stack'  
    let z = mathFn x y
    push z stack''      

// вытолкнуть вершину стека
// применить к ней унарную операцию
// положить результат в стек
let unary f stack = 
    let x,stack' = pop stack  
    push (f x) stack'         

// ==============================================
// Ядро (остальное)
// ==============================================

/// Вытолкнуть и напечатать вершину стека
let SHOW stack = 
    let x,_ = pop stack
    printfn "The answer is %f" x
    stack  // продолжаем с тем же стеком

/// Дублировать вершину стека
let DUP stack = 
    let x,s = pop stack  
    push x (push x s)   
    
/// Обменять два верхних значения местами
let SWAP stack = 
    let x,s = pop stack  
    let y,s' = pop s
    push y (push x s')   

/// Удалить вершину стека
let DROP stack = 
    let _,s = pop stack  //вытолкнуть вершину стека
    s                    //вернуть всё остальное

// ==============================================
// Слова, построенные на примитивах
// ==============================================

// Костанты
// -------------------------------
let EMPTY = StackContents []
let START  = EMPTY


// Числа
// -------------------------------
let ONE = push 1.0
let TWO = push 2.0
let THREE = push 3.0
let FOUR = push 4.0
let FIVE = push 5.0

// MАрифметические функции
// -------------------------------
let ADD = binary (+)
let SUB = binary (-)
let MUL = binary (*)
let DIV = binary (../)

let NEG = unary (fun x -> -x)


// ==============================================
// Слова, построенные с помощью композиции
// ==============================================

let SQUARE =  
    DUP >> MUL 

let CUBE = 
    DUP >> DUP >> MUL >> MUL 

let SUM_NUMBERS_UPTO = 
    DUP                     // n  
    >> ONE >> ADD           // n+1
    >> MUL                  // n(n+1)
    >> TWO >> SWAP >> DIV   // n(n+1) / 2 
```

## Summary | Заключение

> So there we have it, a simple stack based calculator. We've seen how we can start with a few primitive operations (`push`, `pop`, `binary`, `unary`) and from them, build up a whole domain specific language that is both easy to implement and easy to use.

У нас получился простой калькулятор на основе стека. Мы увидели, как, начиная с нескольких примитивных операций (`push`, `pop`, `binary`, `unary`) и других, можно построить полноценный DSL, простой в реализации и использовании.

> As you might guess, this example is based heavily on the Forth language. I highly recommend the free book ["Thinking Forth"](http://thinking-forth.sourceforge.net/), which is not just about the Forth language, but about (*non* object-oriented!) problem decomposition techniques which are equally applicable to functional programming.

Как можно догадаться, данный пример был в изрядной степени основан на языке Forth. Я очень рекомендую бесплатную книгу ["Thinking Forth"](http://thinking-forth.sourceforge.net/), которая повествует не только о языке Forth, но и о других (_не_ объектно-ориентированных!) методах декомпозиции задач, которые одинаково применимы к функциональному программированию в целом.

> I got the idea for this post from a great blog by [Ashley Feniello](http://blogs.msdn.com/b/ashleyf/archive/2011/04/21/programming-is-pointless.aspx). If you want to go deeper into emulating a stack based language in F#, start there. Have fun! 

Я почерпнул идею для данной статьи из великолепного блога [Ashley Feniello](http://blogs.msdn.com/b/ashleyf/archive/2011/04/21/programming-is-pointless.aspx). Если есть желание погрузиться глубже в эмуляцию основанного на стеке языка на базе F#, начните с него. _Have fun!_
