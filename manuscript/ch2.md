# Функционально-Лёгкий JavaScript
# Глава 2: Природа Функций

Функциональное программирование - это **не только программирование с ключевым словом `функция`**. Ох, если бы это было так просто - я мог бы закончить эту книгу прямо здесь! Тем не менее, функции действительно в центре ФП. И как мы используем функции - это то, что делает наш код *функциональным*. 

Но насколько вы уверены, что знаете, что на самом деле означает *функция*?

В этой главе, мы заложим основы для всей книги, исследуя все фундаментальные аспекты функций. На самом деле, это обзор всего, что должны знать о функциях даже не ФП программисты. Несомненно, если мы хотим извлечь максимальную пользу из концептов ФП, нам очень важно знать функции вдоль и поперёк. 

Приготовьтесь, потому что в функции заложено гораздо больше, чем вы, возможно, представляли.

## Что Такое Функция?

На вопрос "Что такое функция?" на первый взгляд ответ кажется очевидным: функция - это набор кода, который может быть выполнен один или несколько раз.

В то время как, это определение оправдано, ему не хватает какой-то очень важной сущности, которая является ядром *функции* применительно к ФП. Итак, давайте копнём глубже, чтобы лучше разобраться в функциях.

### Краткий Математический Обзор

Я знаю, что обещал держаться подальше от математики насколько это возможно, но потерпите немного, поскольку мы быстро рассмотрим некоторые фундаментальные вещи о функциях и графиках из алгебры, прежде чем продолжим.

Вы помните, как учили что-нибудь о `f(x)` ещё в школе? Как насчёт уравнения `y = f(x)`?

Давайте представим, что уравнение определено вот так: <code>f(x) = 2x<sup>2</sup> + 3</code>. Что это значит? Как это уравнение будет выглядеть на графике? Вот график: 

<p align="center">
    <img src="images/fig1.png" width="40%">
</p>

Вы можете заметить, что для любого значения `x`, скажем, `2`, если вы добавите его в уравнение, вы получите `11`. Хотя что такое `11`? Это *возвращаемое значение* функции `f(x)`, которая, как мы говорили ранее, представляет значение `y`.

Другими словами, мы можем толковать входные и выходные значения как точку `(2,11)` на этой кривой графика. И для каждого значения `x`, которое мы подключаем, мы получаем другое значение `y`, которое соединяется с ним в качестве координаты точки. Другое - `(0,3)`, а другое - `(-1,5)`. Сложите все эти точки вместе, и у вас получится график этой параболы, как показано здесь.

Так какое отношнение всё это имеет к ФП?

В математике функция всегда берёт входные значения и всегда возвращает выходные значения. Термин, который вы часто будете слышать вокруг ФП, - это "морфизм"; это причудливый способ описания  набора значений, который сопоставляется с другим набором значений, например, входы функции, связанные с выходами этой функции.

В алгебрe эти входные и выходные данные часто толкуются как составные координат, подлежащих отображению на графике. Однако в наших программах мы можем определять функции со всеми видами входных и выходных данных, хотя они редко будут интерпретироваться как визуально построенная кривая на графике.

### Функция vs Процедуры

Так к чему весь этот разговор о математике и графиках? Потому что, по сути, Функциональное Программирование заключается в том, чтобы использовать функции как *функции* в этом математическом смысле.

Может быть вам привычнее думать о функциях как о процедурах. В чём разница? Процедура - это произвольный набор функциональных возможностей. У неё может быть входные данные, а может и нет.У неё может быть вывод (`return` значение), а может и нет.

Функция принимает входные данные и, безусловно, всегда имеет возвращаемое значение (`return`).

Если вы планируете заниматься Функциональным Программированием, **вы должны использовать функции как можно чаще**, и пытаться избегать процедуры, где это возможно. Все ваши `функции` должны брать входные данные и возвращать выводные.

Почему? Ответ на этот вопрос будет иметь много значений, которые мы будем раскрывать на протяжении всей этой книги.

## Ввод Функции

Пока что мы можем сделать вывод, что функции должны ожидать ввода. Но давайте углубимся в то, как работают входные данные функций.

Иногда вы слышите, как люди называют эти входные данные "аргументами", а иногда и "параметрами". Так что же всё это значит?

*Аргументы* - это значения, которые вы передаёте, а *параметры* - это именованные переменные внутри функции, которые получают эти переданные значения. Пример:

```js
function foo(x,y) {
    // ..
}

var a = 3;

foo( a, a * 2 );
```

`a` и `a * 2` (на самом деле, результат `a * 2`, который равен `6`) являются *аргументами* для вызова `foo(..)`. `x` и `y` являются *параметрами*, которые получают значения аргументов (`3` и `6`, соответвственно).

**Примечание:** В JavaScript нет требования, чтобы количество *аргументов* соответствовало количеству *параметров*. Если вы передаёте больше *аргументов*, чем объявлено *параметров* для их получения, значения без проблем передаются нетронутыми. Доступ к этим значениям можно получить несколькими различными способами, включая старый-добрый объект `arguments`, о котором вы, возможно, слышали раньше. Если вы передаёте меньше *аргументов*, чем объявленных *параметров*, каждый несовпадающий параметр обрабатывается как "undefined" (неопределённая) переменная, что означает, что он присутствует и доступен в области видимости функции, но просто начинается с пустого значения `undefined`.  

### Параметры по Умолчанию

Начиная с ES6, параметры могут объявлять *значения по умолчанию*. В случае, когда аргумент для параметра не передан или ему передано значение `undefined`, он заменяется выражением присвоенным по умолчанию.

Рассмотрите:

```js
function foo(x = 3) {
    console.log( x );
}

foo();                  // 3
foo( undefined );       // 3
foo( null );            // null
foo( 0 );               // 0
```
Всегда полезно подумать о любых случаях по умолчанию, которые могут улучшить использование ваших функций. Однако параметры по умолчанию могут привести к большей сложности с точки зрения чтения и понимания вариантов вызова функции. Будьте благоразумны в том, насколько вы полагаетесь на эту функциональность.

### Подсчёт Входных Данных

Число аргументов, которые "ожидает" функция -- сколько аргументов вы, вероятно, захотите передать ей -- определено числом объявленных параметров:  

```js
function foo(x,y,z) {
    // ..
}
```
`foo(..)` *ожидает* три аргумента, потому что у неё три объявленных параметра. У этого счёта есть особый термин: арность. Арность - это количество параметров, объявленных в функции. Арность `foo(..)` это `3`.

Кроме того, функция с арностью 1 также называется "унарной", функция с арностью 2 также называется "двоичной" (бинарной), а функция с арностью 3 или выше называется "n-арной".

Возможно, вы захотите проверить ссылку на функцию во время выполнения программы, чтобы определить её арность. Это можно сделать с помощью свойства `length` (длина) этой ссылки на функцию:

```js
function foo(x,y,z) {
    // ..
}

foo.length;             // 3
```

Одной из причин определения арности во время выполнения было бы, если бы фрагмент кода получил ссылку на функцию из нескольких источников и отправил разные значения в зависимости от арности каждого из них.

Например, представьте случай где `fn` ссылка на функцию может ожидать один, два, или три аргумента, но вы всегда хотите просто передать переменную `x` в последней позиции:

```js
// `fn` is set to some function reference
// `x` exists with some value

if (fn.length == 1) {
    fn( x );
}
else if (fn.length == 2) {
    fn( undefined, x );
}
else if (fn.length == 3) {
    fn( undefined, undefined, x );
}
```

**Совет:** Свойство функции `length` (длина) доступно только для чтения, и оно определяется в момент объявления функции. Его следует рассматривать, по сути, как фрагмент метаданных, который описывает что-то о предполагаемом использовании функции.

Следует иметь в виду, что некоторые виды изменений списка параметров могут привести к тому, что свойство функции `length` сообщит о чём-то отличном от того, что вы могли бы ожидать:

```js
function foo(x,y = 2) {
    // ..
}

function bar(x,...args) {
    // ..
}

function baz( {a,b} ) {
    // ..
}

foo.length;             // 1
bar.length;             // 1
baz.length;             // 1
```

Как насчёт подсчёта количества аргументов, полученных текущим вызовом функции? Когда-то это было тривиально, но сейчас ситуация немного сложнее. У каждой функции есть доступный объект `arguments` (аргументы) (подобный массиву), который содержит ссылку на каждый из переданных аргументов. Вы можете исследовать свойство `length` (длины) `arguments` (аргументов), чтобы выяснить, сколько их было на самом деле передано:

```js
function foo(x,y,z) {
    console.log( arguments.length );
}

foo( 3, 4 );    // 2
```

Начиная с ES5 (в особенности в строгом режиме - strict mode), некоторые считают `arguments` своего рода устаревшим объектом; многие по возможности избегают его использования. В JS мы "никогда" не нарушаем обратную совместимость, независимо от того, насколько это может быть полезно для будущего прогресса, поэтому `arguments` никогда не будут удалены. Однако сейчас повсеместно рекомендуется избегать его использования, когда это возможно.

Несмотря на это, я предлагаю, что `arguments.length`, и только это, можно продолжать использовать в случаях, когда вам важно количество отправленных аргументов. Будущая версия JS, возможно, добавит функцию, которая позволит определить количество аргментов, без использования `arguments.length`; если это случится, тогда мы можем полностью перестать использовать `arguments`!   

Будьте осторожны: **никогда** не запрашивайте доступ к аргументам позиционно, например, `arguments[1]`. Придерживайтесь только `arguments.length`, и только в случаях, когда вы должны.

Except, how will you access an argument that was passed in a position beyond the declared parameters? I'll answer that in a moment; but first, take a step back and ask yourself, "Why would I want to do that?" Seriously. Think about that closely for a minute.

It should be pretty rare that this occurs; it shouldn't be something you regularly expect or rely on when writing your functions. If you find yourself in such a scenario, spend an extra 20 minutes trying to design the interaction with that function in a different way. Name that extra argument even if it's exceptional.

A function signature that accepts an indeterminate amount of arguments is referred to as a variadic function. Some people prefer this style of function design, but I think you'll find that often the FPer wants to avoid these where possible.

OK, enough harping on that point.

Say you do need to access the arguments in a positional array-like way, possibly because you're accessing an argument that doesn't have a formal parameter at that position. How do we do it?

ES6 to the rescue! Let's declare our function with the `...` operator -- variously referred to as "spread", "rest", or (my preference) "gather":

```js
function foo(x,y,z,...args) {
    // ..
}
```

See the `...args` in the parameter list? That's an ES6 declarative form that tells the engine to collect (ahem, "gather") all remaining arguments (if any) not assigned to named parameters, and put them in a real array named `args`. `args` will always be an array, even if it's empty. But it **will not** include values that are assigned to the `x`, `y`, and `z` parameters, only anything else that's passed in beyond those first three values:

```js
function foo(x,y,z,...args) {
    console.log( x, y, z, args );
}

foo();                  // undefined undefined undefined []
foo( 1, 2, 3 );         // 1 2 3 []
foo( 1, 2, 3, 4 );      // 1 2 3 [ 4 ]
foo( 1, 2, 3, 4, 5 );   // 1 2 3 [ 4, 5 ]
```

So, if you *really* want to design a function that can account for an arbitrary number of arguments to be passed in, use `...args` (or whatever name you like) on the end. Now, you'll have a real, non-deprecated, non-yucky array to access those argument values from.

Just pay attention to the fact that the value `4` is at position `0` of that `args`, not position `3`. And its `length` value won't include those three `1`, `2`, and `3` values. `...args` gathers everything else, not including the `x`, `y`, and `z`.

You *can* use the `...` operator in the parameter list even if there's no other formal parameters declared:

```js
function foo(...args) {
    // ..
}
```

Now `args` will be the full array of arguments, whatever they are, and you can use `args.length` to know exactly how many arguments have been passed in. And you're safe to use `args[1]` or `args[317]` if you so choose. Please don't pass in 318 arguments, though.

### Arrays of Arguments

What if you wanted to pass along an array of values as the arguments to a function call?

```js
function foo(...args) {
    console.log( args[3] );
}

var arr = [ 1, 2, 3, 4, 5 ];

foo( ...arr );                      // 4
```

Our new friend `...` is used, but now not just in the parameter list; it's also used in the argument list at the call-site. It has the opposite behavior in this context. In a parameter list, we said it *gathered* arguments together. In an argument list, it *spreads* them out. So the contents of `arr` are actually spread out as individual arguments to the `foo(..)` call. Do you see how that's different from just passing in a reference to the whole `arr` array?

By the way, multiple values and `...` spreadings can be interleaved, as you see fit:

```js
var arr = [ 2 ];

foo( 1, ...arr, 3, ...[4,5] );      // 4
```

Think of `...` in this symmetric sense: in a value-list position, it *spreads*. In an assignment position -- like a parameter list, because arguments get *assigned to* parameters -- it *gathers*.

Whichever behavior you invoke, `...` makes working with arrays of arguments much easier. Gone are the days of `slice(..)`, `concat(..)`, and `apply(..)` to wrangle our argument value arrays.

**Tip:** Actually, these methods are not entirely useless. There will be a few places we rely on them throughout the code in this book. But certainly in most places, `...` will be much more declaratively readable, and preferable as a result.

### Parameter Destructuring

Consider the variadic `foo(..)` from the previous section:

```js
function foo(...args) {
    // ..
}

foo( ...[1,2,3] );
```

What if we wanted to change that interaction so the caller of our function passes in an array of values instead of individual argument values? Just drop the two `...` usages:

```js
function foo(args) {
    // ..
}

foo( [1,2,3] );
```

Simple enough. But what if now we wanted to give a parameter name to each of the first two values in the passed-in array? We aren't declaring individual parameters anymore, so it seems we lost that ability.

Thankfully, ES6 destructuring is the answer. Destructuring is a way to declare a *pattern* for the kind of structure (object, array, etc.) that you expect to see, and how decomposition (assignment) of its individual parts should be processed.

Consider:

<a name="funcparamdestr"></a>

```js
function foo( [x,y,...args] = [] ) {
    // ..
}

foo( [1,2,3] );
```

Do you spot the `[ .. ]` brackets around the parameter list now? This is called array parameter destructuring.

In this example, destructuring tells the engine that an array is expected in this assignment position (aka parameter). The pattern says to take the first value of that array and assign to a local parameter variable called `x`, the second to `y`, and whatever is left is *gathered* into `args`.

### The Importance of Declarative Style

Considering the destructured `foo(..)` we just looked at, we could instead have processed the parameters manually:

```js
function foo(params) {
    var x = params[0];
    var y = params[1];
    var args = params.slice( 2 );

    // ..
}
```

But here we highlight a principle we only briefly introduced in [Chapter 1](ch1.md/#readability): declarative code communicates more effectively than imperative code.

Declarative code (for example, the destructuring in the former `foo(..)` snippet, or the `...` operator usages) focuses on what the outcome of a piece of code should be.

Imperative code (such as the manual assignments in the latter snippet) focuses more on how to get the outcome. If you later read such imperative code, you have to mentally execute all of it to understand the desired outcome. The outcome is *coded* there, but it's not as clear because it's clouded by the details of *how* we get there.

The earlier `foo(..)` is regarded as more readable, because the destructuring hides the unnecessary details of *how* to manage the parameter inputs; the reader is free to focus only on *what* we will do with those parameters. That's clearly the most important concern, so it's what the reader should be focused on to understand the code most completely.

Wherever possible, and to whatever degrees our language and our libraries/frameworks will let us, **we should be striving for declarative, self-explanatory code.**

## Named Arguments

Just as we can destructure array parameters, we can destructure object parameters:

```js
function foo( {x,y} = {} ) {
    console.log( x, y );
}

foo( {
    y: 3
} );                    // undefined 3
```

We pass in an object as the single argument, and it's destructured into two separate parameter variables `x` and `y`, which are assigned the values of those corresponding property names from the object passed in. It didn't matter that the `x` property wasn't on the object; it just ended up as a variable with `undefined` like you'd expect.

But the part of parameter object destructuring I want you to pay attention to is the object being passed into `foo(..)`.

With a normal call-site like `foo(undefined,3)`, position is used to map from argument to parameter; we put the `3` in the second position to get it assigned to a `y` parameter. But at this new kind of call-site where parameter destructuring is involved, a simple object-property indicates which parameter (`y`) the argument value `3` should be assigned to.

We didn't have to account for `x` in *that* call-site because in effect we didn't care about `x`. We just omitted it, instead of having to do something distracting like passing `undefined` as a positional placeholder.

Some languages have an explicit feature for this: named arguments. In other words, at the call-site, labeling an input value to indicate which parameter it maps to. JavaScript doesn't have named arguments, but parameter object destructuring is the next best thing.

Another FP-related benefit of using an object destructuring to pass in potentially multiple arguments is that a function that only takes one parameter (the object) is much easier to compose with another function's single output. Much more on that in [Chapter 4](ch4.md).

### Unordered Parameters

Another key benefit is that named arguments, by virtue of being specified as object properties, are not fundamentally ordered. That means we can specify inputs in whatever order we want:

```js
function foo( {x,y} = {} ) {
    console.log( x, y );
}

foo( {
    y: 3
} );                    // undefined 3
```

We're skipping the `x` parameter by simply omitting it. Or we could specify an `x` argument if we cared to, even if we listed it after `y` in the object literal. The call-site is no longer cluttered by ordered-placeholders like `undefined` to skip a parameter.

Named arguments are much more flexible, and attractive from a readability perspective, especially when the function in question can take three, four, or more inputs.

**Tip:** If this style of function arguments seems useful or interesting to you, check out coverage of my [FPO library in Appendix C](apC.md/#bonus-fpo).

## Function Output

Let's shift our attention from a function's inputs to its output.

In JavaScript, functions always return a value. These three functions all have identical `return` behavior:

```js
function foo() {}

function bar() {
    return;
}

function baz() {
    return undefined;
}
```

The `undefined` value is implicitly `return`ed if you have no `return` or if you just have an empty `return;`.

But keeping as much with the spirit of FP function definition as possible -- using functions and not procedures -- our functions should always have outputs, which means they should explicitly `return` a value, and usually not `undefined`.

A `return` statement can only return a single value. So if your function needs to return multiple values, your only viable option is to collect them into a compound value like an array or an object:

```js
function foo() {
    var retValue1 = 11;
    var retValue2 = 31;
    return [ retValue1, retValue2 ];
}
```

Then, we'll assign `x` and `y` from two respective items in the array that comes back from `foo()`:

```js
var [ x, y ] = foo();
console.log( x + y );           // 42
```

Collecting multiple values into an array (or object) to return, and subsequently destructuring those values back into distinct assignments, is a way to transparently express multiple outputs for a function.

**Tip:** I'd be remiss if I didn't suggest you take a moment to consider if a function needing multiple outputs could be refactored to avoid that, perhaps separated into two or more smaller single-purpose functions? Sometimes that will be possible, sometimes not; but you should at least consider it.

### Early Returns

The `return` statement doesn't just return a value from a function. It's also a flow control structure; it ends the execution of the function at that point. A function with multiple `return` statements thus has multiple possible exit points, meaning that it may be harder to read a function to understand its output behavior if there are many paths that could produce that output.

Consider:

```js
function foo(x) {
    if (x > 10) return x + 1;

    var y = x / 2;

    if (y > 3) {
        if (x % 2 == 0) return x;
    }

    if (y > 1) return y;

    return x;
}
```

Pop quiz: without cheating and running this code in your browser, what does `foo(2)` return? What about `foo(4)`? And `foo(8)`? And `foo(12)`?

How confident are you in your answers? How much mental tax did you pay to get those answers? I got it wrong the first two times I tried to think it through, and I wrote it!

I think part of the readability problem here is that we're using `return` not just to return different values, but also as a flow control construct to quit a function's execution early in certain cases. There are obviously better ways to write that flow control (the `if` logic, etc.), but I also think there are ways to make the output paths more obvious.

**Note:** The answers to the pop quiz are `2`, `2`, `8`, and `13`.

Consider this version of the code:

```js
function foo(x) {
    var retValue;

    if (retValue == undefined && x > 10) {
        retValue = x + 1;
    }

    var y = x / 2;

    if (y > 3) {
        if (retValue == undefined && x % 2 == 0) {
            retValue = x;
        }
    }

    if (retValue == undefined && y > 1) {
        retValue = y;
    }

    if (retValue == undefined) {
        retValue = x;
    }

    return retValue;
}
```

This version is unquestionably more verbose. But I would argue it's slightly simpler logic to follow, because every branch where `retValue` can get set is *guarded* by the condition that checks if it's already been set.

Rather than `return`ing from the function early, we used normal flow control (`if` logic) to determine the `retValue`'s assignment. At the end, we simply `return retValue`.

I'm not unconditionally saying that you should always have a single `return`, or that you should never do early `return`s, but I do think you should be careful about the flow control part of `return` creating more implicitness in your function definitions. Try to figure out the most explicit way to express the logic; that will often be the best way.

### Un`return`ed Outputs

One technique that you've probably used in most code you've written, and maybe didn't even think about it much, is to have a function output some or all of its values by simply changing variables outside itself.

Remember our <code>f(x) = 2x<sup>2</sup> + 3</code> function from earlier in the chapter? We could have defined it like this in JS:

```js
var y;

function f(x) {
    y = (2 * Math.pow( x, 2 )) + 3;
}

f( 2 );

y;                      // 11
```

I know this is a silly example; we could just as easily have `return`d the value instead of setting it into `y` from within the function:

```js
function f(x) {
    return (2 * Math.pow( x, 2 )) + 3;
}

var y = f( 2 );

y;                      // 11
```

Both functions accomplish the same task, so is there any reason we should pick one version over the other? **Yes, absolutely.**

One way to explain the difference is that the `return` in the latter version signals an explicit output, whereas the `y` assignment in the former is an implicit output. You may already have some intuition that guides you in such cases; typically, developers prefer explicit patterns over implicit ones.

But changing a variable in an outer scope, as we did with the `y` assignment inside of `foo(..)`, is just one way of achieving an implicit output. A more subtle example is making changes to non-local values via reference.

Consider:

```js
function sum(list) {
    var total = 0;
    for (let i = 0; i < list.length; i++) {
        if (!list[i]) list[i] = 0;

        total = total + list[i];
    }

    return total;
}

var nums = [ 1, 3, 9, 27, , 84 ];

sum( nums );            // 124
```

The most obvious output from this function is the sum `124`, which we explicitly `return`ed. But do you spot the other output? Try that code and then inspect the `nums` array. Now do you spot the difference?

Instead of an `undefined` empty slot value in position `4`, now there's a `0`. The harmless looking `list[i] = 0` operation ended up affecting the array value on the outside, even though we operated on a local `list` parameter variable.

Why? Because `list` holds a reference-copy of the `nums` reference, not a value-copy of the `[1,3,9,..]` array value. JavaScript uses references and reference-copies for arrays, objects, and functions, so we may create an accidental output from our function all too easily.

This implicit function output has a special name in the FP world: side effects. And a function that has *no side effects* also has a special name: pure function. We'll talk a lot more about these in [Chapter 5](ch5.md), but the punchline is that we'll want to prefer pure functions and avoid side effects wherever possible.

## Functions of Functions

Functions can receive and return values of any type. A function that receives or returns one or more other function values has the special name: higher-order function.

Consider:

```js
function forEach(list,fn) {
    for (let v of list) {
        fn( v );
    }
}

forEach( [1,2,3,4,5], function each(val){
    console.log( val );
} );
// 1 2 3 4 5
```

`forEach(..)` is a higher-order function because it receives a function as an argument.

A higher-order function can also output another function, like:

```js
function foo() {
    return function inner(msg){
        return msg.toUpperCase();
    };
}

var f = foo();

f( "Hello!" );          // HELLO!
```

`return` is not the only way to "output" an inner function:

```js
function foo() {
    return bar( function inner(msg){
        return msg.toUpperCase();
    } );
}

function bar(func) {
    return func( "Hello!" );
}

foo();                  // HELLO!
```

Functions that treat other functions as values are higher-order functions by definition. FPers write these all the time!

### Keeping Scope

One of the most powerful things in all of programming, and especially in FP, is how a function behaves when it's inside another function's scope. When the inner function makes reference to a variable from the outer function, this is called closure.

Defined pragmatically:

> Closure is when a function remembers and accesses variables from outside of its own scope, even when that function is executed in a different scope.

Consider:

```js
function foo(msg) {
    var fn = function inner(){
        return msg.toUpperCase();
    };

    return fn;
}

var helloFn = foo( "Hello!" );

helloFn();              // HELLO!
```

The `msg` parameter variable in the scope of `foo(..)` is referenced inside the inner function. When `foo(..)` is executed and the inner function is created, it captures the access to the `msg` variable, and retains that access even after being `return`ed.

Once we have `helloFn`, a reference to the inner function, `foo(..)` has finished and it would seem as if its scope should have gone away, meaning the `msg` variable would no longer exist. But that doesn't happen, because the inner function has a closure over `msg` that keeps it alive. The closed over `msg` variable survives for as long as the inner function (now referenced by `helloFn` in a different scope) stays around.

Let's look at a few more examples of closure in action:

```js
function person(name) {
    return function identify(){
        console.log( `I am ${name}` );
    };
}

var fred = person( "Fred" );
var susan = person( "Susan" );

fred();                 // I am Fred
susan();                // I am Susan
```

The inner function `identify()` has closure over the parameter `name`.

The access that closure enables is not restricted to merely reading the variable's original value -- it's not just a snapshot but rather a live link. You can update the value, and that new current state remains remembered until the next access:

```js
function runningCounter(start) {
    var val = start;

    return function current(increment = 1){
        val = val + increment;
        return val;
    };
}

var score = runningCounter( 0 );

score();                // 1
score();                // 2
score( 13 );            // 15
```

**Warning:** For reasons that we'll explore in more depth later in the book, this example of using closure to remember a state that changes (`val`) is probably something you'll want to avoid where possible.

If you have an operation that needs two inputs, one of which you know now but the other will be specified later, you can use closure to remember the first input:

```js
function makeAdder(x) {
    return function sum(y){
        return x + y;
    };
}

// we already know `10` and `37` as first inputs, respectively
var addTo10 = makeAdder( 10 );
var addTo37 = makeAdder( 37 );

// later, we specify the second inputs
addTo10( 3 );           // 13
addTo10( 90 );          // 100

addTo37( 13 );          // 50
```

Normally, a `sum(..)` function would take both an `x` and `y` input to add them together. But in this example we receive and remember (via closure) the `x` value(s) first, while the `y` value(s) are separately specified later.

**Note:** This technique of specifying inputs in successive function calls is very common in FP, and comes in two forms: partial application and currying. We'll dive into them more thoroughly in [Chapter 3](ch3.md/#some-now-some-later).

Of course, since functions are just values in JS, we can remember function values via closure:

```js
function formatter(formatFn) {
    return function inner(str){
        return formatFn( str );
    };
}

var lower = formatter( function formatting(v){
    return v.toLowerCase();
} );

var upperFirst = formatter( function formatting(v){
    return v[0].toUpperCase() + v.substr( 1 ).toLowerCase();
} );

lower( "WOW" );             // wow
upperFirst( "hello" );      // Hello
```

Instead of distributing/repeating the `toUpperCase()` and `toLowerCase()` logic all over our code, FP encourages us to create simple functions that encapsulate -- a fancy way of saying wrapping up -- that behavior.

Specifically, we create two simple unary functions `lower(..)` and `upperFirst(..)`, because those functions will be much easier to wire up to work with other functions in the rest of our program.

**Tip:** Did you spot how `upperFirst(..)` could have used `lower(..)`?

We'll use closure heavily throughout the rest of the text. It may just be the most important foundational practice in all of FP, if not programming as a whole. Make sure you're really comfortable with it!

## Syntax

Before we move on from this primer on functions, let's take a moment to discuss their syntax.

More than many other parts of this text, the discussions in this section are mostly opinion and preference, whether you agree with the views presented here or take opposite ones. These ideas are highly subjective, though many people seem to feel rather absolutely about them.

Ultimately, you get to decide.

### What's in a Name?

Syntactically speaking, function declarations require the inclusion of a name:

```js
function helloMyNameIs() {
    // ..
}
```

But function expressions can come in both named and anonymous forms:

```js
foo( function namedFunctionExpr(){
    // ..
} );

bar( function(){    // <-- look, no name!
    // ..
} );
```

What exactly do we mean by anonymous, by the way? Specifically, functions have a `name` property that holds the string value of the name the function was given syntactically, such as `"helloMyNameIs"` or `"namedFunctionExpr"`. This `name` property is most notably used by the console/developer tools of your JS environment to list the function when it participates in a stack trace (usually from an exception).

Anonymous functions are generally displayed as `(anonymous function)`.

If you've ever had to debug a JS program from nothing but a stack trace of an exception, you probably have felt the pain of seeing `(anonymous function)` appear line after line. This listing doesn't give a developer any clue whatsoever as to the path the exception came from. It's not doing the developer any favors.

If you name your function expressions, the name is always used. So if you use a good name like `handleProfileClicks` instead of `foo`, you'll get much more helpful stack traces.

As of ES6, anonymous function expressions are in certain cases aided by *name inferencing*. Consider:

```js
var x = function(){};

x.name;         // x
```

If the engine is able to guess what name you *probably* want the function to take, it will go ahead and do so.

But beware, not all syntactic forms benefit from name inferencing. Probably the most common place a function expression shows up is as an argument to a function call:

```js
function foo(fn) {
    console.log( fn.name );
}

var x = function(){};

foo( x );               // x
foo( function(){} );    //
```

When the name can't be inferred from the immediate surrounding syntax, it remains an empty string. Such a function will be reported as `(anonymous function)` in a stack trace should one occur.

There are other benefits to a function being named besides the debugging question. First, the syntactic name (aka lexical name) is useful for internal self-reference. Self-reference is necessary for recursion (both sync and async) and also helpful with event handlers.

Consider these different scenarios:

```js
// sync recursion:
function findPropIn(propName,obj) {
    if (obj == undefined || typeof obj != "object") return;

    if (propName in obj) {
        return obj[propName];
    }
    else {
        for (let prop of Object.keys( obj )) {
            let ret = findPropIn( propName, obj[prop] );
            if (ret !== undefined) {
                return ret;
            }
        }
    }
}
```

```js
// async recursion:
setTimeout( function waitForIt(){
    // does `it` exist yet?
    if (!o.it) {
        // try again later
        setTimeout( waitForIt, 100 );
    }
}, 100 );
```

```js
// event handler unbinding
document.getElementById( "onceBtn" )
    .addEventListener( "click", function handleClick(evt){
        // unbind event
        evt.target.removeEventListener( "click", handleClick, false );

        // ..
    }, false );
```

In all these cases, the named function's lexical name was a useful and reliable self-reference from inside itself.

Moreover, even in simple cases with one-liner functions, naming them tends to make code more self-explanatory and thus easier to read for those who haven't read it before:

```js
people.map( function getPreferredName(person){
    return person.nicknames[0] || person.firstName;
} )
// ..
```

The function name `getPreferredName(..)` tells the reader something about what the mapping operation is intending to do that is not entirely obvious from just its code. This name label helps the code be more readable.

Another place where anonymous function expressions are common is with immediately invoked function expressions (IIFEs):

```js
(function(){

    // look, I'm an IIFE!

})();
```

You virtually never see IIFEs using names for their function expressions, but they should. Why? For all the same reasons we just went over: stack trace debugging, reliable self-reference, and readability. If you can't come up with any other name for your IIFE, at least use the word IIFE:

```js
(function IIFE(){

    // You already knew I was an IIFE!

})();
```

What I'm getting at is there are multiple reasons why **named functions are always more preferable to anonymous functions.** As a matter of fact, I'd go so far as to say that there's basically never a case where an anonymous function is more preferable. They just don't really have any advantage over their named counterparts.

It's incredibly easy to write anonymous functions, because it's one less name we have to devote our mental attention to figuring out.

I'll be honest; I'm as guilty of this as anyone. I don't like to struggle with naming. The first few names I come up with for a function are usually bad. I have to revisit the naming over and over. I'd much rather just punt with a good ol' anonymous function expression.

But we're trading ease-of-writing for pain-of-reading. This is not a good trade-off. Being lazy or uncreative enough to not want to figure out names for your functions is an all too common, but poor, excuse for using anonymous functions.

**Name every single function.** And if you sit there stumped, unable to come up with a good name for some function you've written, I'd strongly suggest you don't fully understand that function's purpose yet -- or it's just too broad or abstract. You need to go back and re-design the function until this is more clear. And by that point, a name will become more apparent.

In my practice, if I don't have a good name to use for a function, I name it `TODO` initially. I'm certain that I'll at least catch that later when I search for "TODO" comments before committing code.

I can testify from my own experience that in the struggle to name something well, I usually have come to understand it better, later, and often even refactor its design for improved readability and maintainability.

This time investment is well worth it.

### Functions Without `function`

So far we've been using the full canonical syntax for functions. But you've no doubt also heard all the buzz around the ES6 `=>` arrow function syntax.

Compare:

```js
people.map( function getPreferredName(person){
    return person.nicknames[0] || person.firstName;
} );

// vs.

people.map( person => person.nicknames[0] || person.firstName );
```

Whoa.

The keyword `function` is gone, so is `return`, the parentheses (`( )`), the curly braces (`{ }`), and the innermost semicolon (`;`). In place of all that, we used a so-called fat arrow symbol (`=>`).

But there's another thing we omitted. Did you spot it? The `getPreferredName` function name.

That's right; `=>` arrow functions are lexically anonymous; there's no way to syntactically provide it a name. Their names can be inferred like regular functions, but again, the most common case of function expression values passed as arguments won't get any assistance in that way. Bummer.

If `person.nicknames` isn't defined for some reason, an exception will be thrown, meaning this `(anonymous function)` will be at the top of the stack trace. Ugh.

Honestly, the anonymity of `=>` arrow functions is a `=>` dagger to the heart, for me. I cannot abide by the loss of naming. It's harder to read, harder to debug, and impossible to self-reference.

But if that wasn't bad enough, the other slap in the face is that there's a whole bunch of subtle syntactic variations that you must wade through if you have different scenarios for your function definition. I'm not going to cover all of them in detail here, but briefly:

```js
people.map( person => person.nicknames[0] || person.firstName );

// multiple parameters? need ( )
people.map( (person,idx) => person.nicknames[0] || person.firstName );

// parameter destructuring? need ( )
people.map( ({ person }) => person.nicknames[0] || person.firstName );

// parameter default? need ( )
people.map( (person = {}) => person.nicknames[0] || person.firstName );

// returning an object? need ( )
people.map( person =>
    ({ preferredName: person.nicknames[0] || person.firstName })
);
```

The case for excitement over `=>` in the FP world is primarily that it follows almost exactly from the mathematical notation for functions, especially in FP languages like Haskell. The shape of `=>` arrow function syntax communicates mathematically.

Digging even further, I'd suggest that the argument in favor of `=>` is that by using much lighter-weight syntax, we reduce the visual boundaries between functions which lets us use simple function expressions much like we'd use lazy expressions -- another favorite of the FPer.

I think most FPers are going to wave off the concerns I'm sharing. They love anonymous functions and they love saving on syntax. But like I said before: you decide.

**Note:** Though I do not prefer to use `=>` in practice in my production code, they are useful in quick code explorations. Moreover, we will use arrow functions in many places throughout the rest of this book -- especially when we present typical FP utilities -- where conciseness is preferred to optimize for the limited physical space in code snippets. Make your own determinations whether this approach will make your own production-ready code more or less readable.

## What's This?

If you're not familiar with the `this` binding rules in JavaScript, I recommend checking out my book *You Don't Know JS: this & Object Prototypes*. For the purposes of this section, I'll assume you know how `this` gets determined for a function call (one of the four rules). But even if you're still fuzzy on *this*, the good news is we're going to conclude that you shouldn't be using `this` if you're trying to do FP.

**Note:** We're tackling a topic that we'll ultimately conclude we shouldn't use. Why!? Because the topic of `this` has implications for other topics covered later in this book. For example, our notions of function purity are impacted by `this` being essentially an implicit input to a function (see [Chapter 5](ch5.md)). Additionally, our perspective on `this` affects whether we choose array methods (`arr.map(..)`) versus standalone utilities (`map(..,arr)`) (see [Chapter 9](ch9.md)). Understanding `this` is essential to understanding why `this` really should *not* be part of your FP!

JavaScript `function`s have a `this` keyword that's automatically bound per function call. The `this` keyword can be described in many different ways, but I prefer to say it provides an object context for the function to run against.

`this` is an implicit parameter input for your function.

Consider:

```js
function sum() {
    return this.x + this.y;
}

var context = {
    x: 1,
    y: 2
};

sum.call( context );        // 3

context.sum = sum;
context.sum();              // 3

var s = sum.bind( context );
s();                        // 3
```

Of course, if `this` can be input into a function implicitly, the same object context could be sent in as an explicit argument:

```js
function sum(ctx) {
    return ctx.x + ctx.y;
}

var context = {
    x: 1,
    y: 2
};

sum( context );
```

Simpler. And this kind of code will be a lot easier to deal with in FP. It's much easier to wire multiple functions together, or use any of the other input wrangling techniques we will get into in the next chapter, when inputs are always explicit. Doing them with implicit inputs like `this` ranges from awkward to nearly impossible depending on the scenario.

There are other tricks we can leverage in a `this`-based system, including prototype-delegation (also covered in detail in *You Don't Know JS: this & Object Prototypes*):

```js
var Auth = {
    authorize() {
        var credentials = `${this.username}:${this.password}`;
        this.send( credentials, resp => {
            if (resp.error) this.displayError( resp.error );
            else this.displaySuccess();
        } );
    },
    send(/* .. */) {
        // ..
    }
};

var Login = Object.assign( Object.create( Auth ), {
    doLogin(user,pw) {
        this.username = user;
        this.password = pw;
        this.authorize();
    },
    displayError(err) {
        // ..
    },
    displaySuccess() {
        // ..
    }
} );

Login.doLogin( "fred", "123456" );
```

**Note:** `Object.assign(..)` is an ES6+ utility for doing a shallow assignment copy of properties from one or more source objects to a single target object: `Object.assign( target, source1, ... )`.

In case you're having trouble parsing what this code does: we have two separate objects `Login` and `Auth`, where `Login` performs prototype-delegation to `Auth`. Through delegation and the implicit `this` context sharing, these two objects virtually compose during the `this.authorize()` function call, so that properties/methods on `this` are dynamically shared with the `Auth.authorize(..)` function.

*This* code doesn't fit with various principles of FP for a variety of reasons, but one of the obvious hitches is the implicit `this` sharing. We could be more explicit about it and keep code closer to FP-friendly style:

```js
// ..

authorize(ctx) {
    var credentials = `${ctx.username}:${ctx.password}`;
    Auth.send( credentials, function onResp(resp){
        if (resp.error) ctx.displayError( resp.error );
        else ctx.displaySuccess();
    } );
}

// ..

doLogin(user,pw) {
    Auth.authorize( {
        username: user,
        password: pw
    } );
}

// ..
```

From my perspective, the problem is not with using objects to organize behavior. It's that we're trying to use implicit input instead of being explicit about it. When I'm wearing my FP hat, I want to leave `this` stuff on the shelf.

## Summary

Functions are powerful.

But let's be clear what a function is. It's not just a collection of statements/operations. Specifically, a function needs one or more inputs (ideally, just one!) and an output.

Functions inside of functions can have closure over outer variables and remember them for later. This is one of the most important concepts in all of programming, and a fundamental foundation of FP.

Be careful of anonymous functions, especially `=>` arrow functions. They're convenient to write, but they shift the cost from author to reader. The whole reason we're studying FP here is to write more readable code, so don't be so quick to jump on that bandwagon.

Don't use `this`-aware functions. Just don't.

You should now be developing a clear and colorful perspective in your mind of what *function* means in Functional Programming. It's time to start wrangling functions to get them to interoperate, and the next chapter teaches you a variety of critical techniques you'll need along the way.
