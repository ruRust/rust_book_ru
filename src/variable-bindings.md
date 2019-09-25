% Связывание имён

Любая реальная программа на Rust посложнее, чем «Hello World», использует
*связывание имён*. Они связывают значение с именем, для того чтобы
воспользоваться им позже. Для связывания используется команда `let`:

```rust
fn main() {
    let x = 5;
}
```

Все операции, производимые ниже, будут происходить в функции `main()`, так как
каждый раз вставлять в примеры `fn main() {` немного утомляет. Убедитесь, что
примеры, приведённые в этом разделе, вы вводите в функцию `main()`, иначе можете
получить ошибку при компиляции.

# Шаблоны

Во многих языках программирования связывание имён называется *переменной*. Но у
связывания имен в Rust есть пара трюков в рукаве. В левой части выражения
`let` располагается не просто имя переменной, а "[шаблон][pattern]". Это значит,
что мы можем делать вещи вроде этой:

```rust
let (x, y) = (1, 2);
```

После завершения этого выражения `x` будет единицей, a `y` — двойкой. Шаблоны
очень мощны, и о них написана отдельная [глава][pattern]. Но на данный момент
нам не нужны эти возможности, так что мы просто будем помнить о них и пойдём
дальше.

[pattern]: patterns.html

# Указание типов

Rust — статически типизированный язык программирования, и значит мы должны
указывать типы, и они будут проверяться во время компиляции. Так почему же наш
первый пример скомпилировался? В Rust есть нечто, называемое *выводом типов*.
Если Rust самостоятельно может понять, какой тип у переменной, то он не требует
указывать его.

Тем не менее, мы можем указать желаемый тип. Он следует после двоеточия (`:`):

```rust
let x: i32 = 5;
```

Если бы мы попросили вас прочитать это вслух, вы бы сказали «`x` — это
связывание типа `int` со значением `пять`».

В этом случае мы указали, что `x` у нас будет 32-битным целым числом со знаком.
В Rust есть и другие целочисленные типы. Их имена начинаются с `i` для целых
чисел со знаком и с `u` для целых чисел без знака. Целые числа могут иметь
размер 8, 16, 32 и 64 бита.

В дальнейших примерах мы будем указывать тип в комментариях. Это будет выглядеть
вот так:

```rust
fn main() {
    let x = 5; // x: i32
}
```

Обратите внимание на сходство между этим комментарием и синтаксисом, который вы
используете с `let`. Включение такого типа комментариев не является
идиоматичным для Rust, но иногда мы будем включать их для того, чтобы помочь
вам понять, какие типы будут выведены Rust.

# Изменяемость

По умолчанию, связывание *неизменяемо*. Этот код не скомпилируется:

```rust,ignore
let x = 5;
x = 10;
```

И вы получите ошибку:

```text
error: re-assignment of immutable variable `x`
     x = 10;
     ^~~~~~~
```

Если вы хотите, чтобы связывание было изменяемым, вы можете использовать
модификатор `mut`:

```rust
let mut x = 5; // mut x: i32
x = 10;
```

Может показаться, что незачем делать связывание неизменяемым по умолчанию. Но
вспомните, на чём в первую очередь фокусируется Rust: на безопасности. Если вы
случайно забыли указать `mut` и изменили связывание, компилятор заметит это, и
сообщит вам, что вы попытались изменить не то, что собирались. Если бы по
умолчанию связывание было изменяемым, то в приведённой выше ситуации компилятор
не сможет вам помочь. Если вы намерены изменить значение переменной, то просто
добавьте `mut`.

Есть и другие весомые аргументы в пользу того, чтобы по возможности избегать
изменяемого состояния, но это выходит за рамки данной книги. В общем, зачастую
вы можете избежать явных изменений, и это предпочтительнее в Rust. Тем не менее,
иногда без изменения значения просто не обойтись, так что это не запрещено.

# Инициализация связывания

Связывание имен в Rust имеет ещё одно отличие от других языков: оно
требует инициализации перед использованием.

Давайте приступим к рассмотрению вышесказанного. Измените ваш файл `src/main.rs`
так, что бы он выглядел следующим образом:

```rust
fn main() {
    let x: i32;

    println!("Hello world!");
}
```

Используйте команду `cargo build` в командной строке, чтобы собрать проект. Вы
должны получить предупреждение, но программа будет работать и будет выводить
строку «Привет, мир!»:

```text
   Compiling hello_world v0.0.1 (file:///home/you/projects/hello_world)
src/main.rs:2:9: 2:10 warning: unused variable: `x`, #[warn(unused_variable)]
   on by default
src/main.rs:2     let x: i32;
                      ^
```

Rust предупредит нас о том, что мы не используем связанную переменную, но от
того, что мы её не используем, не будет никакого вреда, поэтому это не ошибка.
Однако, всё изменится, если мы попробуем использовать `x`. Сделаем это. Измените
вашу программу так, что бы она выглядела следующим образом:

```rust,ignore
fn main() {
    let x: i32;

    println!("x имеет значение {}", x);
}
```

И попробуйте собрать проект. Вы получите ошибку:

```bash
$ cargo build
   Compiling hello_world v0.0.1 (file:///home/you/projects/hello_world)
src/main.rs:4:39: 4:40 error: use of possibly uninitialized variable: `x`
src/main.rs:4     println!("x имеет значение {}", x);
                                                    ^
note: in expansion of format_args!
<std macros>:2:23: 2:77 note: expansion site
<std macros>:1:1: 3:2 note: in expansion of println!
src/main.rs:4:5: 4:42 note: expansion site
error: aborting due to previous error
Could not compile `hello_world`.
```

Rust не позволит использовать неинициализированную переменную. Далее, поговорим
о `{}`, которые мы добавили в `println!`.

Если вы добавите две фигурные скобки (`{}`, иногда называемые «усами»...) в вашу
печатаемую строку, Rust истолкует это как просьбу вставки некоторого значения.
*Строковая интерполяция* — это термин в информатике, который обозначает
«вставить посреди строки». Мы добавили запятую, и затем `x`, чтобы указать, что
мы хотим вставить `x` в строку. Запятая используется для разделения параметров,
если в функцию или макрос передаётся больше одного параметра.

При вставке переменной в строку, Rust проверит её тип и попытается отобразить
осмысленное значение. Если вы хотите указать формат более детально, то можете
ознакомиться с [доступными способами форматирования строк (англ.)][format]. На
данный момент мы просто используем способ по умолчанию: печатать целые числа не
очень сложно.

[format]: http://doc.rust-lang.org/std/fmt/index.html

# Область видимости и затенение

Вернёмся к связыванию. Связанные имена имеют область видимости — они
ограничены блоком, в котором они были объявлены. Блок — это совокупность
операторов, заключённая в фигурных скобках (`{` и `}`). Функции тоже объявляются
с помощью блоков! Сейчас мы объявим два связанных имени, `x` и `y`, которые
существуют в различных блоках. К `x` можно получить доступ внутри блока
`fn main() {}`, в то же время `y` доступен только во внутреннем блоке:

```rust,ignore
fn main() {
    let x: i32 = 17;
    {
        let y: i32 = 3;
        println!("Значение x равно {} и значение y равно {}", x, y);
    }
    // Ошибка компиляции
    println!("Значение x равно {} и значение y равно {}", x, y);
}
```

Первый `println!` выведет "Значение x равно 17 и значение y равно 3", но этот
пример не скомпилируется, потому что второй `println!` не может получить доступ
к значению `y`, оно вне области видимости. Вместо этого мы увидим ошибку:

```bash
$ cargo build
   Compiling hello v0.1.0 (file:///home/you/projects/hello_world)
main.rs:8:62: 8:63 error: unresolved name `y`. Did you mean `x`? [E0425]
main.rs:8     println!("Значение x равно {} и значение y равно {}", x, y);
                                                                       ^
<std macros>:2:25: 2:56 note: in this expansion of format_args!
<std macros>:3:1: 3:54 note: in this expansion of print! (defined in <std macros>)
main.rs:8:5: 8:65 note: in this expansion of println! (defined in <std macros>)
main.rs:8:62: 8:63 help: run `rustc --explain E0425` to see a detailed explanation
error: aborting due to previous error
Could not compile `hello`.

To learn more, run the command again with --verbose.
```

Помимо прочего, связанные имена могут быть затенены. Это значит, что, если
объявить связывание с именем, которое уже существует, то оно переопределит
предыдущее.

```rust
let x: i32 = 8;
{
    println!("{}", x); // Выводит "8"
    let x = 12;
    println!("{}", x); // Выводит "12"
}
println!("{}", x); // Выводит "8"
let x =  42;
println!("{}", x); // Выводит "42"
```

Затенение и изменяемое связывание могут казаться двумя сторонами одной монеты, но
они являются двумя отдельными концепциями, которые не всегда взаимозаменяемы.
Например, затенение позволяет связать имя с другим типом или изменить связь с
изменяемой на неизменяемую.

```rust
let mut x: i32 = 1;
x = 7;
let x = x; // теперь X неизменяемое и связанно с 7

let y = 4;
let y = "Я так же могу быть связан с текстом!"; // теперь Y другого типа
```