---
layout: post
title: "Organizing functions"
description: "Nested functions and modules"
nav: thinking-functionally
seriesId: "Thinking functionally"
seriesOrder: 10
categories: [Functions, Modules]
---

> Now that you know how to define functions, how can you organize them?

Теперь Вы знаете как определять функции, но как организовать их?

> In F#, there are three options:

В F# существуют три варианта:

> * functions can be nested inside other functions.
> * at an application level, the top level functions are grouped into "modules".
> * alternatively, you can also use the object-oriented approach and attach functions to types as methods.

* функции могут быть вложены в другие функции.
* на уровне приложения, функции верхнего уровня группируются по "модулям".
* в качестве альтернативы, можно придерживаться ООП и прикреплять функции к типам в качестве методов.

> We'll look at the first two options in this post, and the third in the next post.

В данной статье будут рассмотрены первые два способа, оставшийся будет разобран в следующей.

## Nested Functions | Вложенные функции ##

> In F#, you can define functions inside other functions. This is a great way to encapsulate "helper" functions that are needed for the main function but shouldn't be exposed outside.

В F# можно определять функции внутри других функций. Это хороший способ инкапсулировать сопутствующие функции, которые необходимы лишь для основной функции и не должны быть представлены наружу.

> In the example below `add` is nested inside `addThreeNumbers`:

В примере ниже `add` вложен в `addThreeNumbers`:

```fsharp
let addThreeNumbers x y z  = 

    //create a nested helper function
    let add n = 
       fun x -> x + n
       
    // use the helper function       
    x |> add y |> add z

// test
addThreeNumbers 2 3 4
```

> A nested function can access its parent function parameters directly, because they are in scope. 
> So, in the example below, the `printError` nested function does not need to have any parameters of its own -- it can access the `n` and `max` values directly.

Вложенные функции могут получить доступ к родительским параметрам напрямую, потому-что они находятся в области видимости.
Так, в приведенном ниже примере вложенная функция`printError` не нуждается в параметрах, т.к. она может получить доступ к `n` и `max` напрямую.

```fsharp
let validateSize max n  = 

    //create a nested helper function with no params
    let printError() = 
        printfn "Oops: '%i' is bigger than max: '%i'" n max

    // use the helper function               
    if n > max then printError()

// test
validateSize 10 9
validateSize 10 11
```

> A very common pattern is that the main function defines a nested recursive helper function, and then calls it with the appropriate initial values.
> The code below is an example of this:

Очень распространенным паттерном является основная функция определяющая вложенную рекурсивную вспомогательную функцию, которая вызывается с соответствующими начальными значениями.
Ниже приведен подобный пример:

```fsharp
let sumNumbersUpTo max = 

    // recursive helper function with accumulator    
    let rec recursiveSum n sumSoFar = 
        match n with
        | 0 -> sumSoFar
        | _ -> recursiveSum (n-1) (n+sumSoFar)

    // call helper function with initial values
    recursiveSum max 0
            
// test
sumNumbersUpTo 10
```


> When nesting functions, do try to avoid very deeply nested functions, especially if the nested functions directly access the variables in their parent scopes rather than having parameters passed to them.
> A badly nested function will be just as confusing as the worst kind of deeply nested imperative branching.

Старайтесь избегать глубокой вложенности, особенно в случаях прямого доступа (не в виле параметров) к родительским переменным.
Плохо вложенные функции будут столь же сложны для понимания, как худшие виды глубоких вложенных императивных ветвлений.

> Here's how *not* to do it:

Пример того как *нельзя* делать:

```fsharp
// wtf does this function do?
let f x = 
    let f2 y = 
        let f3 z = 
            x * z
        let f4 z = 
            let f5 z = 
                y * z
            let f6 () = 
                y * x
            f6()
        f4 y
    x * f2 x
```


## Modules | Модули ##

> A module is just a set of functions that are grouped together, typically because they work on the same data type or types.

Модуль - это просто набор функций, которые сгруппированы вместе, обычно потому что они работают с одним и тем же типом или типами данных.

> A module definition looks very like a function definition. It starts with the `module` keyword, then an `=` sign, and then the contents of the module are listed.
> The contents of the module *must* be indented, just as expressions in a function definition must be indented.

Определение модуля очень похоже на определение функции. Оно начинается с ключевого слова `module`, затем идет знак `=`, после чего идет содержимое модуля.
Содержимое модуля *должно* быть смещено, также как выражения в определении функций.

> Here's a module that contains two functions:

Определение модуля содержащего две функции:

```fsharp
module MathStuff = 

    let add x y  = x + y
    let subtract x y  = x - y
```

> Now if you try this in Visual Studio, and you hover over the `add` function, you will see that the full name of the `add` function is actually `MathStuff.add`, just as if `MathStuff` was a class and `add` was a method.

Если попробовать этот код в Visual Studio, то при наведении на `add`, можно увидеть полное имя `add`, которое в действительности равно `MathStuff.add`, как будто `MastStuff` был классом, а `add` методом.

> Actually, that's exactly what is going on. Behind the scenes, the F# compiler creates a static class with static methods. So the C# equivalent would be:

В действительности, именно это и происходит. За кулисами F# компилятор создает статический класс со статическими методами. C# эквивалент выглядел бы так:

```csharp
static class MathStuff
{
    static public int add(int x, int y)
    {
        return x + y;
    }

    static public int subtract(int x, int y)
    {
        return x - y;
    }
}
```

> If you realize that modules are just static classes, and that functions are static methods, then you will already have a head-start on understanding how modules work in F#,
> as most of the rules that apply to static classes also apply to modules.

Осознание того, что модули являются всего-лишь статическими классами, а функции являются статическими методами, даст хорошее понимание того, как модули работают в F#, большинство правил применимых к статическим классам также применимо и к модулям.

> And, just as in C# every standalone function must be part of a class, in F# every standalone function *must* be part of a module.

И так же, как в C# каждая отдельно стоящая функция должна быть частью класса, в F# каждая отдельно стоящая функция *должна* быть частью модуля.

### Accessing functions across module boundaries | Доступ к функциям за пределами модуля

> If you want to access a function in another module, you can refer to it by its qualified name.

Если есть необходимость получить функцию из другого модуля, можно ссылаться на нее через полное имя.

```fsharp
module MathStuff = 

    let add x y  = x + y
    let subtract x y  = x - y

module OtherStuff = 

    // use a function from the MathStuff module
    let add1 x = MathStuff.add x 1  
```

> You can also import all the functions in another module with the `open` directive, 
> after which you can use the short name, rather than having to specify the qualified name.

Так же можно импортировать все функции другого модуля посредством директивы `open`, после чего можно будет использовать короткое имя вместо полного.

```fsharp
module OtherStuff = 
    open MathStuff  // make all functions accessible

    let add1 x = add x 1
```

> The rules for using qualified names are exactly as you would expect. That is, you can always use a fully qualified name to access a function, 
> and you can use relative names or unqualified names based on what other modules are in scope.

Правила использования имен вполне ожидаемые. Всегда можно обратиться к функции по ее полному имени, и можно использовать относительные или неполные имена в зависимости от текущей области видимости.

### Nested modules | Вложенные модули

> Just like static classes, modules can contain child modules nested within them, as shown below:

Как и статические классы, модули могут содержать вложенные модули:

```fsharp
module MathStuff = 

    let add x y  = x + y
    let subtract x y  = x - y

    // nested module    
    module FloatLib = 

        let add x y :float = x + y
        let subtract x y :float  = x - y
```
        
> And other modules can reference functions in the nested modules using either a full name or a relative name as appropriate:

Другие модули могут ссылаться на функции во вложенных модулях используя полное или относительное имя в зависимости от обстоятельств:

```fsharp
module OtherStuff = 
    open MathStuff

    let add1 x = add x 1

    // fully qualified
    let add1Float x = MathStuff.FloatLib.add x 1.0
    
    //with a relative path
    let sub1Float x = FloatLib.subtract x 1.0
```

### Top level modules | Модули высшего порядка

> So if there can be nested child modules, that implies that, going back up the chain, there must always be some *top-level* parent module.  This is indeed true.

// TODO: Разобраться с термином.
Таким образом, могут существовать дочерние модули, это означает, что идя назад по цепочке можно дойти до некоего родительского модуля высшего порядка. Это действительно так.

> Top level modules are defined slightly differently than the modules we have seen so far. 

Модули верхнего уровня определяются несколько иначе в отличии от модулей, которые были показаны ранее.

> * The `module MyModuleName` line *must* be the first declaration in the file 
> * There is no `=` sign
> * The contents of the module are *not* indented

* Строка `module MyModuleName` *должна* быть первой декларацией в файле
* Знак `=` отсутствует
* Содержимое модуля *не* должно иметь отступа

> In general, there must be a top level module declaration present in every `.FS` source file. There some exceptions, but it is good practice anyway.
The module name does not have to be the same as the name of the file, but two files cannot share the same module name.

В общем случае, должна существовать декларация верхнего уровня в каждом исходном `.FS` файле. Есть исключения, но это хорошая практика в любом случае. Имя модуля не обязано совпадать с именем файла, но два файла не могут содержать модули с одинаковыми именами.

> For `.FSX` script files, the module declaration is not needed, in which case the module name is automatically set to the filename of the script.

Для `.FSX` файлов, декларация модуля не нужна, в данном случае имя модуля автоматически устанавливается из имени файла скрипта.

> Here is an example of `MathStuff` declared as a top level module:

Пример `MathStuff` декларированного в качестве верхнего модуля:

```fsharp
// top level module
module MathStuff

let add x y  = x + y
let subtract x y  = x - y

// nested module    
module FloatLib = 

    let add x y :float = x + y
    let subtract x y :float  = x - y
```

> Note the lack of indentation for the top level code (the contents of `module MathStuff`), but that the content of a nested module like `FloatLib` does still need to be indented.

Обратите внимание на отсутствие отступа в коде высшего уровня (содержимом `module MathStuff`), но содержимое вложенного модуля `FloatLib` все еще обязано иметь отступ.

### Other module content | Другое содержимое модулей

> A module can contain other declarations as well as functions, including type declarations, simple values and initialization code (like static constructors)

Кроме функций модули могут содержать другие объявления, такие как декларации типов, простые значения и инициализирующий код (как статический конструктор)

```fsharp
module MathStuff = 

    // functions
    let add x y  = x + y
    let subtract x y  = x - y

    // type definitions
    type Complex = {r:float; i:float}
    type IntegerFunction = int -> int -> int
    type DegreesOrRadians = Deg | Rad

    // "constant"
    let PI = 3.141

    // "variable"
    let mutable TrigType = Deg

    // initialization / static constructor
    do printfn "module initialized"

```

> <div class="alert alert-info">By the way, if you are playing with these examples in the interactive window, you might want to right-click and do "Reset Session" every so often, so that the code is fresh and doesn't get contaminated with previous evaluations</div>

<div class="alert alert-info">Кстати, если Вы запускаете данные примеры в интерактивном режиме, Вам может понадобиться достаточно часто перезапускать сессию, чтобы код оставался "свежим" и не заражался предыдущими вычислениями.</div>

### Shadowing | Сокрытие

> Here's our example module again. Notice that `MathStuff` has an `add` function and `FloatLib` *also* has an `add` function.

Вот еще один пример. Обратите внимание, что `MathStuff` содержит `add` _также_ как и `FloatLib`.

```fsharp
module MathStuff = 

    let add x y  = x + y
    let subtract x y  = x - y

    // nested module    
    module FloatLib = 

        let add x y :float = x + y
        let subtract x y :float  = x - y
```

> Now what happens if I bring *both* of them into scope, and then use `add`?

Что случится, если открыть *оба* модуля в текущем области видимости и вызвать `add`?

```fsharp
open  MathStuff
open  MathStuff.FloatLib

let result = add 1 2  // Compiler error: This expression was expected to 
                      // have type float but here has type int    
```

> What happened was that the `MathStuff.FloatLib` module has masked or overridden the original `MathStuff` module, which has been "shadowed" by `FloatLib`.

Случилось то, что модуль `MathStuff.FloatLib` переопределил оригинальный `MathStuff`, который был сокрыт ("shadowed") `FloatLib`.

> As a result you now get a [FS0001 compiler error](../troubleshooting-fsharp/index.md#FS0001) because the first parameter `1` is expected to be a float. You would have to change `1` to `1.0` to fix this.

В результате была получена [FS0001 compiler error](../troubleshooting-fsharp/index.md#FS0001), потому что первый параметр `1` ожидался как float. Потребуется изменить `1` на `1.0`, чтобы исправить это.

> Unfortunately, this is invisible and easy to overlook. Sometimes you can do cool tricks with this, almost like subclassing, but more often, it can be annoying if you have functions with the same name (such as the very common `map`).

К сожалению, на практике это _невидимо_ и легко упускается из виду. Иногда можно делать интересные трюки с помощью данного приема, почти как подклассы, но в большинстве своем, наличие одноименных функций раздражает (например, очень распространенная функция `map`).

> If you don't want this to happen, there is a way to stop it by using the `RequireQualifiedAccess` attribute. Here's the same example where both modules are decorated with it.

Если требуется избежать данного поведения, существует способ пресечь его при помощи атрибута `RequireQualifiedAccess`. Тот же пример у которого оба модуля декорированы данными атрибутом:

```fsharp
[<RequireQualifiedAccess>]
module MathStuff = 

    let add x y  = x + y
    let subtract x y  = x - y

    // nested module    
    [<RequireQualifiedAccess>]    
    module FloatLib = 

        let add x y :float = x + y
        let subtract x y :float  = x - y
```

> Now the `open` isn't allowed:

Теперь директива `open` недоступна:
        
```fsharp
open  MathStuff   // error
open  MathStuff.FloatLib // error
```

> But we can still access the functions (without any ambiguity) via their qualified name:

Но все еще можно получить доступ к функциям (без какой-либо двусмысленности) через их полные имена:

```fsharp
let result = MathStuff.add 1 2  
let result = MathStuff.FloatLib.add 1.0 2.0
```


### Access Control | Контроль доступа

> F# supports the use of standard .NET access control keywords such as `public`, `private`, and `internal`.
> The [MSDN documentation](http://msdn.microsoft.com/en-us/library/dd233188) has the complete details.

F# поддерживает использование стандартных .NET операторов контроля доступа, таких как `public`, `private` и `internal`. [MSDN документация](http://msdn.microsoft.com/en-us/library/dd233188) содержит полную информацию.

> * These access specifiers can be put on the top-level ("let bound") functions, values, types and other declarations in a module. They can also be specified for the modules themselves (you might want a private nested module, for example).
> * Everything is public by default (with a few exceptions) so you will need to use `private` or `internal` if you want to protect them.

* Эти спецификаторы доступа могут быть применены к ("let bound") функциям верхнего уровня, значениям, типам и другим декларациям в модуле. Они также могут быть указаны для самих модулей (например может понадобиться приватный вложенный модуль).
* По умолчанию все имеет публичный доступ (за исключением нескольких случаев), поэтому для их защиты потребуется использовать `private` или `internal`.

> These access specifiers are just one way of doing access control in F#. Another completely different way is to use module "signature" files, which are a bit like C header files. They describe the content of the module in an abstract way. Signatures are very useful for doing serious encapsulation, but that discussion will have to wait for the planned series on encapsulation and capability based security.

Данные спецификаторы доступа являются лишь одним из способов управления видимостью в F#. Другим совершенно отличным способом является использование файлов "сигнатуры" модуля, которые напоминают файлы заголовков C. Они определяют содержимое модуля абстрактным способом. Сигнатуры очень полезны для серьезных инкапсуляций, но для рассмотрения их возможностей придется дождаться запланированной серии по инкапсуляции и *оcнованной на возможностях безопасности*.

## Namespaces | Пространства имен

> Namespaces in F# are similar to namespaces in C#.  They can be used to organize modules and types to avoid name collisions.

Пространства имен в F# похожи на пространства из C#. Они могут быть использованы для организации модулей и типов, чтобы избежать конфликтов имен.

> A namespace is declared with a `namespace` keyword, as shown below.

Пространство имен декларированное с помощью ключевого слова `namespace`:

```fsharp
namespace Utilities

module MathStuff = 

    // functions
    let add x y  = x + y
    let subtract x y  = x - y
```

> Because of this namespace, the fully qualified name of the `MathStuff` module now becomes `Utilities.MathStuff` and
> the fully qualified name of the `add` function now becomes `Utilities.MathStuff.add`.

Из-за данного пространств имен полное имя модуля `MathStuff` стало `Utilities.MathStuff`, а полное имя `add` - `Utilities.MathStuff.add`.

> With the namespace, the indentation rules apply, so that the module defined above must have its content indented, as it it were a nested module.

К модулям внутри пространства имен применяются те же правила отступа, что были показаны ранее для модулей.

> You can also declare a namespace implicitly by adding dots to the module name. That is, the code above could also be written as:

Также можно объявлять пространство имен явно при помощи добавления точки в имени модуля. Т.е. код выше можно переписать так:

```fsharp
module Utilities.MathStuff  

// functions
let add x y  = x + y
let subtract x y  = x - y
```

> The fully qualified name of the `MathStuff` module is still `Utilities.MathStuff`, but
> in this case, the module is a top-level module and the contents do not need to be indented.

Полное имя модуля `MathStuff` все еще `Utilities.MathStuff`, но теперь это модуль верхнего уровня и его содержимому не нужен отступ.

> Some additional things to be aware of when using namespaces:

Некоторые дополнительные особенности использования пространств имен:

> * Namespaces are optional for modules. And unlike C#, there is no default namespace for an F# project, so a top level module without a namespace will be at the global level.
> If you are planning to create reusable libraries, be sure to add some sort of namespace to avoid naming collisions with code in other libraries. 
> * Namespaces can directly contain type declarations, but not function declarations. As noted earlier, all function and value declarations must be part of a module.
> * Finally, be aware that namespaces don't work well in scripts.  For example, if you try to to send a namespace declaration such as `namespace Utilities` below to the interactive window, you will get an error.

* Пространства имен необязательны для модулей. В отличии от C#, не существует пространства имен по умолчанию для F# проекта, так что модуль верхнего уровня без пространства имен будет иметь глобальный уровень. Если планируется создание библиотеки многоразового использования, необходимо добавить несколько пространств имен чтобы избежать коллизий имен с кодом других библиотек.
* Пространства имен непосредственно содержат декларации типов, но не декларации функции. Как было замечено ранее, все объявления функций и значений должны быть частью модуля.
* Наконец, следует иметь ввиду, что пространства имен не работают в скриптах. Например, если попробовать отправить объявление пространства имен, например `namespace Utilities` будет получена ошибка. 


### Namespace hierarchies | Иерархия пространств имен

> You can create a namespace hierarchy by simply separating the names with periods:

Можно создавать иерархию пространств имен, просто разделив имена точками:

```fsharp
namespace Core.Utilities

module MathStuff = 
    let add x y  = x + y
```

> And if you want to put *two* namespaces in the same file, you can. Note that all namespaces *must* be fully qualified -- there is no nesting.

Можно объявить *два* пространства имен в одном файле, если есть такое желание. Следует обратить внимание, что все пространства имен *должны* быть объявлены полным именем -- они не поддерживают вложенность.

```fsharp
namespace Core.Utilities

module MathStuff = 
    let add x y  = x + y
    
namespace Core.Extra

module MoreMathStuff = 
    let add x y  = x + y
```

> One thing you can't do is have a naming collision between a namespace and a module.

Конфликт имен между пространством имен и модулем невозможен.

```fsharp
namespace Core.Utilities

module MathStuff = 
    let add x y  = x + y
    
namespace Core

// fully qualified name of module
// is Core.Utilities  
// Collision with namespace above!
module Utilities = 
    let add x y  = x + y
```


## Mixing types and functions in modules | Смешивание типов и функций в модулях ##

> We've seen that a module typically consists of a set of related functions that act on a data type.  

Как мы видели ранее, модули обычно состоят из множества взаимозависимых функций, которые взаимодействуют с определенным типом данных.

> In an object oriented program, the data structure and the functions that act on it would be combined in a class.
> However in functional-style F#, a data structure and the functions that act on it are combined in a module instead.

В ООП структуры данных и функции воздействующие на них были бы объединены вместе внутри класса. Однако в функциональном стиле, структуры данных и функции на них воздействующие будут объединены внутри модуля.

> There are two common patterns for mixing types and functions together:

Существуют два паттерна комбинирования типов и функций вместе:

> * having the type declared separately from the functions 
> * having the type declared in the same module as the functions 

* тип объявляется отдельно от функций
* тип объявляется в том же модуле, что и функции

> In the first approach, the type is declared *outside* any module (but in a namespace) and then the functions that work on the type
are put in a module with a similar name.

В первом случае тип декларируется *за пределами* какого либо модуля (но в пространстве имен), после чего функции, которые работают с этим типом, помещаются в одноименный типу модуль.

```fsharp
// top-level module
namespace Example

// declare the type outside the module
type PersonType = {First:string; Last:string}

// declare a module for functions that work on the type
module Person = 

    // constructor
    let create first last = 
        {First=first; Last=last}

    // method that works on the type
    let fullName {First=first; Last=last} = 
        first + " " + last

// test
let person = Person.create "john" "doe" 
Person.fullName person |> printfn "Fullname=%s"
```

> In the alternative approach, the type is declared *inside* the module and given a simple name such as "`T`" or the name of the module. 
> So the functions are accessed with names like `MyModule.Func1` and `MyModule.Func2` while the type itself is
> accessed with a name like `MyModule.T`. Here's an example:

В альтернативном варианте, тип декларируется *внутри* модуля и имеет простое название, типа "`T`" или имя модуля. Доступ к функциям осуществляется приблизительно так: `MyModule.Func` и `MyModule.Func2`, а доступ к типу: `MyModule.T`:

```fsharp
module Customer = 

    // Customer.T is the primary type for this module
    type T = {AccountId:int; Name:string}

    // constructor
    let create id name = 
        {T.AccountId=id; T.Name=name}

    // method that works on the type
    let isValid {T.AccountId=id; } = 
        id > 0

// test
let customer = Customer.create 42 "bob" 
Customer.isValid customer |> printfn "Is valid?=%b"
```

> Note that in both cases, you should have a constructor function that creates new instances of the type (a factory method, if you will),
> Doing this means that you will rarely have to explicitly name the type in your client code, and therefore, you not should not care whether it lives in the module or not!

Заметьте, что в обоих случаях должна быть функция создающая новый экземпляр типа (фабрика). Это позволит не вызывать тип явным образом, и можно будет не задаваться вопросом, находится ли тип внутри модуля.

> So which approach should you choose?

Какой способ лучше?

> * The former approach is more .NET like, and much better if you want to share your libraries with other non-F# code, as the exported class names are what you would expect.
> * The latter approach is more common for those used to other functional languages. The type inside a module compiles into nested classes, which is not so nice for interop.  

* Первый подход больше похож на классический .NET, и его следует предпочесть, если планируется использовать данную библиотеку для кода за пределами F#, где ожидают отдельно существующий класс.
* Второй подход является более распространенным в других функциональных языках. Тип внутри модуля компилируется как вложенный класс, что как правило не очень удобно для ООП языков.

> For yourself, you might want to experiment with both. And in a team programming situation, you should choose one style and be consistent. 

Для себя, вы можете поэкспериментировать с обоими. В случае командной разработки надлежит выбрать один стиль.

### Modules containing types only | Модули содержащие только типы

> If you have a set of types that you need to declare without any associated functions, don't bother to use a module. You can declare types directly in a namespace and avoid nested classes.

Если есть множество типов, которые необходимо объявить без каких либо функций, не стоит заморачиваться использованием модуля. Можно объявить типы прямо в пространстве имен не прибегая к вложенным классам.

> For example, here is how you might think to do it:

Например, вы захотите нечто вроде этого:

```fsharp
// top-level module
module Example

// declare the type inside a module
type PersonType = {First:string; Last:string}

// no functions in the module, just types...
```

> And here is a alternative way to do it. The `module` keyword has simply been replaced with `namespace`.

А вот другой способ сделать тоже самое. Слово `module` просто заменяется на слово `namespace`.

```fsharp
// use a namespace 
namespace Example

// declare the type outside any module
type PersonType = {First:string; Last:string}
```

> In both cases, `PersonType` will have the same fully qualified name.

В обоих случаях, `PersonType` будет иметь одно и тоже полное имя.

> Note that this only works with types. Functions must always live in a module.

Следует обратить внимание, что данная замена работает только с типами. Функции **всегда** должны быть внутри модуля.
