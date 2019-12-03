# Игра "Угадай число"

Давайте начнём программирование с Rust, работая вместе над практическим проектом! Эта глава ознакомит вас с некоторыми базовыми концепции языка Rust, показывая их применение в реальной программе.
Вы узнаете про `let`, `match`, методы, ассоциированные функции, использование внешних пакетов и многое другое! Последующие главы расскажут про эти идеи более детально. В этой главе Вы будете практиковать основы.

Мы реализуем простую задачу: угадывание числа. Алгоритм игры следующий: программа
генерирует целое число от 0 до 100. Игрок должен угадать это число. После каждого
неправильного ответа даётся подсказка - загаданное число меньше или больше введенного
игроком. Если число угадано - победитель принимает поздравления. :-) Программа завершает
работу.

## Настройка нового проекта

Для создания нового проекта, в строке терминала перейдите в папку *projects* (в
ту, которую Вы создали ранее). С помощью уже знакомой Вам утилиты `cargo` создадим
новый проект:

```text
$ cargo new guessing_game
$ cd guessing_game
```

Данная команда `cargo new` принимает аргумент - имя нового проекта
`guessing_game`, а далее флаг `--bin`, который уточняет какой тип приложения мы
хотим создать. В данном случае - это консольное приложение.

Посмотрите на созданный файл *Cargo.toml*:

<span class="filename">Файл: Cargo.toml</span>

```toml
[package]
name = "guessing_game"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"

[dependencies]
```

Если информация про авторов, которую Cargo берёт из вашей среды неправильна, исправьте её в файле и сохраните.

As you saw in Chapter 1, `cargo new` generates a “Hello, world!” program for
you. Check out the *src/main.rs* file:

<span class="filename">Файл: src/main.rs</span>

```rust
fn main() {
    println!("Hello, world!");
}
```

Давайте теперь скомпилируем эту программу и запустим ее с помощью команды `cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Hello, world!
```

Используйте команду `run`, когда нужно быстро скомпилировать и запустить программу на
выполнение. Что-то подобное будет происходить и в той программе, которую мы будем
создавать.

Откройте файл *src/main.rs* для редактирования. Далее мы будем менять содержание
этого шаблонного файла исходного кода программы.

## Обработка вводимых данных

Программа начинается с опроса пользователя - необходимо ввести число. Далее программа
анализирует ввёденную пользователем информацию. Для начала напишем код, позволяющий
ввести данные с клавиатуры. Пожалуйста, замените содержание файла исходного кода
*src/main.rs* на следующий текст:

<span class="filename">Файл: src/main.rs</span>

```rust,ignore
use std::io;

fn main() {
    println!("Guess the number!");

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listing 2-1: Программа просит ввести строку, а потом печатает
её.</span>

Этот программный код содержит много новой для Вас информации. Разберём код шаг за
шагом. Для того чтобы считать введённые данные с клавиатуры, а потом вывести их
на экран, нам нужна библиотека ввода/вывода `io`. Эта библиотека входит в состав
стандартной библиотеки Rust `std`.

```rust,ignore
use std::io;
```

По умолчанию, Rust загружает несколько типов данных в память, чтобы их можно было
бы использовать без каких-либо дополнительных описаний в коде ([the *prelude*](https://doc.rust-lang.org/std/prelude/index.html))<comment>.
Если типы данных, которые вы хотите использовать в программе не входят в состав
этих типов данных вам надо описать их использования явным образом. Это можно сделать
с помощью выражения <code data-md-type="codespan">use</code>. Библиотека ввода/вывода <code data-md-type="codespan">std::io</code> предоставляет множество
полезных функциональных возможностей, в том числе для обработки вводимых данных
пользователя.</comment>

Функция `main` - точка начала выполнения программы:

```rust,ignore
fn main() {
```

Синтаксис определения функции следующий: `fn` - ключевое слово начала описания функции,
круглые скобки `()` - контейнер входных параметров функции, фигурная скобка `{` -
обозначение начала тела функции.

`println!` - это макрос, которые печатает текст и перемещает курсор на новую строку:

```rust,ignore
println!("Guess the number!");

println!("Please input your guess.");
```

Этот код печатает подсказку с указанием игры и приглашает пользователя ввести число.

### Хранение данных с помощью переменных

Далее, мы создаём место хранения введенных игроком данных:

```rust,ignore
let mut guess = String::new();
```

Сейчас программа начинает становиться интересной. Обратите, пожалуйста, внимание,
как много нового в этой строке! Прежде всего здесь есть выражение `let`, которое
используется для создания *переменной*. Вот, например:

```rust,ignore
let foo = bar;
```

В этой строке создаётся переменная с именем `foo`, которая связывается со значением
`bar`. Особенностью языка Rust является то, что переменные по умолчанию неизменяемые.
Этот пример показывает, как использовать ключевое слово `mut` перед именем переменной
для того, чтобы сделать переменную изменяемой:

```rust,ignore
let foo = 5; // immutable
let mut bar = 5; // mutable
```

> Обратите внимание, что символы `//` - синтаксис, обозначающий комментарий,
> который размещается на одной строке. Rust игнорирует всё, что размещено в строке комментария. Более подробно см. в главе № 3.

Давайте вернёмся к нашей программе. Теперь вы знаете, что `let mut guess` создаст изменяемую переменную с именем `guess`. С другой стороны от знака равенства ( `=` ) находится значение, к которому `guess` будет привязано и что является результатом вызова функции `String::new`. Эта функция возвращает новый экземпляр типа данных `String` .<a href="../std/string/struct.String.html" data-md-type="link">`String`</a> <comment> </comment> - строковый тип, предоставляемый стандартной библиотекой, который является строкой текста в формате UTF-8, с возможностью увеличения размера.

The `::` syntax in the `::new` line indicates that `new` is an *associated
function* of the `String` type. An associated function is implemented on a type,
in this case `String`, rather than on a particular instance of a `String`. Some
languages call this a *static method*.

This `new` function creates a new, empty string. You’ll find a `new` function
on many types, because it’s a common name for a function that makes a new value
of some kind.

Подытожим, строка `let mut guess = String::new();` создает изменяемую переменную, которая привязывается к пустому новому экземпляру `String`. Ух ты!

Recall that we included the input/output functionality from the standard
library with `use std::io;` on the first line of the program. Now we’ll call
the `stdin` function from the `io` module:

```rust,ignore
io::stdin().read_line(&mut guess)
    .expect("Failed to read line");
```

Если бы мы не добавили строку`use std::io` в начало программы, мы смогли бы вызвать эту функцию как `std::io::stdin`. Функция `stdin` возвращает экземпляр [`std::io::Stdin`](../std/io/struct.Stdin.html), который является типом, предоставляющим обработку стандартного ввода из Вашего терминала.

Следующая часть кода, `.read_line(&mut guess)`, вызывает метод [`read_line`](../std/io/struct.Stdin.html#method.read_line) обработчика стандартного ввода для получения данных от пользователя. Мы передаем в функцию `read_line` один аргумент: `&mut guess`.

The job of `read_line` is to take whatever the user types into standard input
and place that into a string, so it takes that string as an argument. The
string argument needs to be mutable so the method can change the string’s
content by adding the user input.

Символ `&` показывает, что аргумент является *ссылкой*, которая даёт возможность получить доступ к данным в нескольких местах кода без необходимости копировать эти данные в памяти несколько раз. Ссылки - это сложная особенность и одно из главных преимуществ Rust, безопасность и легкость использования ссылок. Вам не нужно знать массу деталей для завершения этой программы. В данный момент нужно знать, что ссылки по умолчанию являются неизменяемыми. Следовательно, необходимо написать `&mut guess`, а не `&guess`, чтобы сделать ссылку изменяемой. (Глава 4 объясняет ссылки более тщательно.)

### Обработка потенциальных ошибок с помощью типа `Result`

We’re not quite done with this line of code. Although what we’ve discussed so
far is a single line of text, it’s only the first part of the single logical
line of code. The second part is this method:

```rust,ignore
.expect("Failed to read line");
```

When you call a method with the `.foo()` syntax, it’s often wise to introduce a
newline and other whitespace to help break up long lines. We could have
written this code as:

```rust,ignore
io::stdin().read_line(&mut guess).expect("Failed to read line");
```

Тем не менее, одна строка читается сложнее, поэтому лучшим решением будет разделить её: две строки для двух вызовов функций. Давайте теперь объясним, что эта строка делает.

Как упоминалось ранее, `read_line` помещает символы  пользовательского ввода в переменную переданную в неё, но она также имеет возвращаемый тип - в этом случае это [`io::Result`](../std/io/type.Result.html). Rust в стандартной библиотеке имеет несколько типов с именем `Result`: обобщенный тип <a href="../std/result/enum.Result.html" data-md-type="link">`Result`</a>, а также конкретные версии для подмодулей, такие как `io::Result`.

The `Result` types are [*enumerations*](ch06-00-enums.html)<comment>, often referred
to as <em data-md-type="emphasis">enums</em>. An enumeration is a type that can have a fixed set of values,
and those values are called the enum’s <em data-md-type="emphasis">variants</em>. Chapter 6 will cover enums
in more detail.</comment>

For `Result`, the variants are `Ok` or `Err`. The `Ok` variant indicates the
operation was successful, and inside `Ok` is the successfully generated value.
The `Err` variant means the operation failed, and `Err` contains information
about how or why the operation failed.

Назначение типа `Result` состоит в кодировании информации для обработки ошибки.
Значения типа`Result`, как и значения любых типов, имеют определённые в них методы. Экземпляр `io::Result` имеет [`expect` метод](../std/result/enum.Result.html#method.expect), который можно вызвать. Если экземпляр `io::Result` является значением `Err`, то метод `expect` вызовет сбой программы и покажет сообщение, которое Вы передали как аргумент в `expect`. Если метод `read_line` вернёт `Err`, это вероятно будет ошибка, происходящая от операционной системы. Если экземпляр `io::Result` будет `Ok`, `expect` возьмёт и вернёт значение содержащееся внутри `Ok`, чтобы его можно было использовать. В этом случае, значением будет число байт, которые пользователь ввел в стандартный поток ввода.

Если Вы не вызовете `expect`, программа скомпилируется, но Вы получите предупреждение:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
warning: unused `std::result::Result` which must be used
  --> src/main.rs:10:5
   |
10 |     io::stdin().read_line(&mut guess);
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
   |
   = note: #[warn(unused_must_use)] on by default
```

Rust предупреждает, что вы не используете значение `Result` возвращённое из `read_line`, показывая что программа не обрабатывает возможную ошибку.

Правильным способом убрать предупреждение будет обработать ошибку, но так как Вы хотите чтобы программа завершилась, Вы можете использовать `expect`. Вы узнаете про восстановление после ошибок в главе 9.

### Вывод значений с помощью `println!`

Помимо закрывающих фигурных скобок присутствует ещё одна строка которую нужно обсудить:

```rust,ignore
println!("You guessed: {}", guess);
```

This line prints the string we saved the user’s input in. The set of curly
brackets, `{}`, is a placeholder: think of `{}` as little crab pincers that
hold a value in place. You can print more than one value using curly brackets:
the first set of curly brackets holds the first value listed after the format
string, the second set holds the second value, and so on. Printing multiple
values in one call to `println!` would look like this:

```rust
let x = 5;
let y = 10;

println!("x = {} and y = {}", x, y);
```

Этот код выведет `x = 5 and y = 10`.

### Тестирование первой части

Давайте протестирует первую часть игры. Запустите её используя `cargo run`:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
Please input your guess.
6
You guessed: 6
```

На данный момент первая часть игры завершена: мы получаем ввод с клавиатуры и затем выводим его.

## Генерация секретного числа

Next, we need to generate a secret number that the user will try to guess. The
secret number should be different every time so the game is fun to play more
than once. Let’s use a random number between 1 and 100 so the game isn’t too
difficult. Rust doesn’t yet include random number functionality in its standard
library. However, the Rust team does provide a [`rand` crate](https://crates.io/crates/rand).

### Using a Crate to Get More Functionality

Remember that a crate is a collection of Rust source code files.
The project we’ve been building is a *binary crate*, which is an executable.
The `rand` crate is a *library crate*, which contains code intended to be
used in other programs.

Cargo’s use of external crates is where it really shines. Before we can write
code that uses `rand`, we need to modify the *Cargo.toml* file to include the
`rand` crate as a dependency. Open that file now and add the following line to
the bottom beneath the `[dependencies]` section header that Cargo created for
you:

<!-- When updating the version of `rand` used, also update the version of
`rand` used in these files so they all match:
* ch07-04-bringing-paths-into-scope-with-the-use-keyword.md
* ch14-03-cargo-workspaces.md
-->

<span class="filename">Файл: Cargo.toml</span>

```toml
[dependencies]
rand = "0.5.5"
```

In the *Cargo.toml* file, everything that follows a header is part of a section
that continues until another section starts. The `[dependencies]` section is
where you tell Cargo which external crates your project depends on and which
versions of those crates you require. In this case, we’ll specify the `rand`
crate with the semantic version specifier `0.5.5`. Cargo understands [Semantic
Versioning](http://semver.org)<comment> (sometimes called <em data-md-type="emphasis">SemVer</em>), which is a
standard for writing version numbers. The number `0.5.5` is actually shorthand
for <code data-md-type="codespan">^0.5.5</code>, which means “any version that has a public API compatible with
version 0.5.5.”</comment>

Давайте теперь соберём наш проект без каких-либо правок кода, как показано в листинге 2-2.

```text
$ cargo build
    Updating crates.io index
  Downloaded rand v0.5.5
  Downloaded libc v0.2.62
  Downloaded rand_core v0.2.2
  Downloaded rand_core v0.3.1
  Downloaded rand_core v0.4.2
   Compiling rand_core v0.4.2
   Compiling libc v0.2.62
   Compiling rand_core v0.3.1
   Compiling rand_core v0.2.2
   Compiling rand v0.5.5
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 s
```

<span class="caption">Листинг 2-2: Вывод работы команды <code>cargo build</code> после добавления пакета rand в зависимости</span>

You may see different version numbers (but they will all be compatible with
the code, thanks to SemVer!), and the lines may be in a different order.

Теперь, когда у нас есть внешняя зависимость, Cargo выбирает последние версии всех крейтов из *реестра*, который является копией данных из [Crates.io](https://crates.io/) . Crates.io - то место, где люди в экосистеме Rust размещают проекты с открытым исходным кодом для других.

After updating the registry, Cargo checks the `[dependencies]` section and
downloads any crates you don’t have yet. In this case, although we only listed
`rand` as a dependency, Cargo also grabbed `libc` and `rand_core`, because `rand`
depends on those to work. After downloading the crates, Rust compiles them and
then compiles the project with the dependencies available.

Если вы сразу же запустите `cargo build` без изменений, то не получите никакого вывода кроме строки `Finished`. Cargo знает, что зависимости скачаны и скомпилированы и вы ничего не изменили в вашем файле {em2}Cargo.toml{/em2}. Cargo также знает, что вы не изменили что-либо в коде, так что он также не будет компилировать снова. Так как нечего делать, он просто выходит.

If you open up the *src/main.rs* file, make a trivial change, and then save it
and build again, you’ll only see two lines of output:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53s
```

Эти строки показывают, что Cargo обновляет только сборку с вашими небольшими изменениями в файле *src/main.rs*. Остальные зависимости не изменились, поэтому Cargo знает, что может
использовать то, что он уже скачал и скомпилировал ранее. Он просто пересобирает часть кода.

#### Ensuring Reproducible Builds with the *Cargo.lock* File

Cargo has a mechanism that ensures you can rebuild the same artifact every time
you or anyone else builds your code: Cargo will use only the versions of the
dependencies you specified until you indicate otherwise. For example, what
happens if next week version 0.5.6 of the `rand` crate comes out and
contains an important bug fix but also contains a regression that will break
your code?

Ответом на эту проблему является файл *Cargo.lock*, который создается впервые при запуске `cargo build` и потом находится в вашем каталоге *guessing_game*. Когда вы впервые собираете проект, Cargo выясняет все версии зависимостей, которые соответствуют критериям, а затем записывает их в файл *Cargo.lock*. Когда в будущем вы будете собирать проект, то Cargo увидит, что файл *Cargo.lock* существует и будет использовать указанные там версии вместо того, чтобы делать всю работу по выяснению версий снова. Это позволяет иметь воспроизводимую сборку автоматически. Другими словами, ваш проект будет оставаться на уровне версии `0.5.5` благодаря *Cargo.lock* файлу, пока не обновите версию явно.

#### Updating a Crate to Get a New Version

Когда вы *хотите* обновить клейт, Cargo предоставляет другую команду, `update`, которая проигнорирует файл *Cargo.lock* и выяснит все последние версии, которые соответствуют вашим спецификациям в *Cargo.toml* файле. Если это работает, Cargo напишет эти версии в файл *Cargo.lock*.

But by default, Cargo will only look for versions greater than `0.5.5` and less
than `0.6.0`. If the `rand` crate has released two new versions, `0.5.6` and
`0.6.0`, you would see the following if you ran `cargo update`:

```text
$ cargo update
    Updating crates.io index
    Updating rand v0.5.5 -> v0.5.6
```

В этот момент вы также заметите изменение в файле *Cargo.lock*, отметив что версия крейта `rand`, которую вы сейчас используете является `0.5.6` .

Если вы хотите использовать крейт `rand` версии `0.6.0` или любую версию в `0.6.x`, то для этого нужно будет обновить файл *Cargo.toml*, чтобы он выглядел следующим образом:

```toml
[dependencies]
rand = "0.6.0"
```

В следующий раз при запуске `cargo build`, Cargo обновит реестр доступных крейтов и пересмотрит ваши требования к `rand` в соответствии с новой версией, которую вы указали.

Можно много рассказать про [Cargo](http://doc.crates.io) и [его экосистему](http://doc.crates.io/crates-io.html) которые мы обсудим в главе 14, сейчас это все что вам нужно знать. Cargo позволяет очень легко переиспользовать библиотеки, поэтому Rust разработчики имеют возможность писать меньшие проекты, которые скомпонованы из многих пакетов.

### Generating a Random Number

Теперь, когда вы добавили `rand` крейт в *Cargo.toml*, давайте начнем использовать `rand`. Следующим шагом является обновление *src/main.rs*, как показано в листинге 2-3.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    println!("The secret number is: {}", secret_number);

    println!("Please input your guess.");

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    println!("You guessed: {}", guess);
}
```

<span class="caption">Listing 2-3: Adding code to generate a random
number</span>

Сначала, добавим `use` строку вида: `use rand::Rng`. Крейт `Rng` определяет методы, которые реализуют генератор случайных чисел и данный типаж должен быть в области видимости, чтобы использовать его методы. Глава 10 подробно расскажет об типажах.

Далее добавляем две строки по середине. Функция `rand::thread_rng` предоставит генератор случайных чисел, который мы собираемся использовать: тот, который является локальным для текущего потока выполнения и инициализирован операционной системой. Затем мы вызываем метод `gen_range` у генератора случайного числа. Этот метод объявлен в типаже `Rng`, который мы импортировали в область действия оператором `use rand::Rng`. Метод `gen_range` принимает два числа в качестве аргументов и генерирует случайное число в их диапазоне. Он включает нижнюю границу, но исключает верхнюю границу, поэтому нам нужно указать `1` и `101` чтобы запросить число от 1 до 100.

> Примечание: вы не будете знать, какие типажи использовать, какие методы и функции вызывать из крейта. Инструкции по использованию крейта есть в каждой документации. Еще одна полезная особенность Cargo - то, что вы можете запустить команду `cargo doc --open`, которая соберет локальную документацию, предоставленную всеми зависимостями и откроет её в браузере. Если вы заинтересованы в других функциях крейта `rand`, например, запустите `cargo doc --open` и нажмите `rand` на боковой панели слева.

Вторая строка, которую мы добавили в середину кода, печатает секретное число. Она полезна, когда при разработке программы, чтобы иметь возможность её тестировать, но мы удалим ее из окончательной версии. Это не интересная игра, если программа сразу печатает ответ при запуске!

Try running the program a few times:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 2.53 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 7
Please input your guess.
4
You guessed: 4
$ cargo run
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 83
Please input your guess.
5
You guessed: 5
```

You should get different random numbers, and they should all be numbers between
1 and 100. Great job!

## Comparing the Guess to the Secret Number

Теперь, когда у нас есть пользовательский ввод и случайное число, можно сравнить их. Этот шаг показан в листинге 2-4. Обратите внимание, что этот код ещё не компилируется, мы объясним.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore,does_not_compile
use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {

    // ---snip---

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

<span class="caption">Listing 2-4: Handling the possible return values of
comparing two numbers</span>

Первый новый код здесь - это еще один оператор `use`, импортирующий в область видимости тип с именем `std::cmp::Ordering` из стандартной библиотеки. Как `Result` , тип `Ordering` - это еще одно перечисление, но вариантами для `Ordering` являются `Less`, `Greater` и `Equal`. Это три результата, которые возможны при сравнении двух значений.

Затем мы добавляем пять новых строк внизу, которые используют тип `Ordering`. Метод `cmp` сравнивает два значения и может быть вызван на чем угодно, что можно сравнивать. Метод принимает ссылку на то, с чем вы хотите сравнить: здесь мы сравниваем `guess` с `secret_number`. Затем он возвращает вариант `Ordering` перечисления присутствующий в области видимости благодаря `use`. Используется [`match`](ch06-02-match.html) выражение для решения, что делать дальше на основе полученного варианта `Ordering` из вызова `cmp` со значениями в `guess` и `secret_number`.

Выражение `match` состоит из *рукавов*. Рукав состоит из *шаблона* и кода, который должен быть запущен, если значение заданное в начале выражение `match` соответствует образцу этого рукава. Rust берёт значение, указанное для `match` и просматривает каждый рукав по очереди. Конструкция `match` и шаблоны являются мощными функциями в Rust, которые позволяют выразить различные ситуации, которые могут встретиться в коде и убедится, что все они обработаны. Эти особенности будут подробно рассмотрены в главе 6 и главе 18 соответственно.

Давайте разберём пример того, что случилось бы с использованным здесь выражением `match`. Скажем, пользователь угадал 50 и случайно сгенерированное секретное число на этот раз равно 38. Когда код сравнивает 50 с 38, метод `cmp` вернёт `Ordering::Greater`, потому что 50 больше 38. Выражение `match` получает значение `Ordering::Greater` и начинает проверять каждый рукав шаблонов. Он смотрит на шаблон первого рукава, `Ordering::Less`, и видит, что значение `Ordering::Greater` не соответствует `Ordering::Less`, поэтому игнорируется код в этом рукаве и переходит к следующему рукаву. Образец следующего рукава, `Ordering::Greater`, шаблон *совпадает* с `Ordering::Greater`! Связанный с рукавом код выполняется и напечатает `Too big!` на экран. Выражение `match`  заканчивается, потому что нет необходимости смотреть на последний рукав в этом сценарии.

However, the code in Listing 2-4 won’t compile yet. Let’s try it:

```text
$ cargo build
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
error[E0308]: mismatched types
  --> src/main.rs:23:21
   |
23 |     match guess.cmp(&secret_number) {
   |                     ^^^^^^^^^^^^^^ expected struct `std::string::String`, found integer
   |
   = note: expected type `&std::string::String`
   = note:    found type `&{integer}`

error: aborting due to previous error
Could not compile `guessing_game`.
```

Суть ошибки состоит в том, что существуют *не совпадающие типы*. В Rust имеется строгая, статическая система типов. Тем не менее, он также имеет выведение типов. Когда мы написали `let mut guess = String::new()`, Rust смог сделать вывод, что `guess` должен быть `String` и не заставил нас писать тип. Но переменная `secret_number` это также числовой тип с другой стороны. Некоторые числовые типы могут иметь значения от 1 до 100: 32-битное знаковое число `i32`; 32-битное без знаковое число `u32`; 64-битное знаковое `i64`; а также другие. Rust по умолчанию использует `i32`, который является типом для `secret_number`, если вы не добавите информацию о типе в другом месте, что заставит Rust вывести другой числовой тип. Причина ошибки в том, что Rust не может сравнить строковый тип и числовой.

В конечном счёте, мы хотим преобразовать считываемые программой `String` из стандартного ввода в тип действительного числа, чтобы было можно сравнивать его в числовом виде с загаданным числом. Можно это сделать, добавив следующие две строки в тело `main` функции:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
// --snip--

    let mut guess = String::new();

    io::stdin().read_line(&mut guess)
        .expect("Failed to read line");

    let guess: u32 = guess.trim().parse()
        .expect("Please type a number!");

    println!("You guessed: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

The two new lines are:

```rust,ignore
let guess: u32 = guess.trim().parse()
    .expect("Please type a number!");
```

Мы создаём переменную с именем `guess`. Но подождите, разве в программе уже нет переменной с именем `guess`? Да это так, но Rust позволяет нам *затенять* предыдущее значение `guess` с помощью нового. Эта функция часто используется в ситуациях где вы хотите преобразовать значение из одного типа в другой. Затенение позволяет повторно использовать имя переменной `guess`, а не заставлять нас создавать две уникальные переменные, вроде `guess_str` и `guess`. (Глава 3 охватывает затенение более подробно.)

Мы связываем `guess` с выражением `guess.trim().parse()`. Переменная `guess` в выражении ссылается на исходную переменную `guess` которая была типа `String` при вводе данных. Метод `trim` на экземпляре `String` удалит все пробелы в начале и конце. Хотя `u32` может содержать только числовые символы, пользователь должен нажать <span class="keystroke">Enter</span>, чтобы удовлетворить метод `read_line`. Когда пользователь нажимает <span class="keystroke">ввод</span>, символ новой строки добавляется в строку. Например, если пользователь вводит <span class="keystroke">5</span> и нажимает <span class="keystroke">ввод</span>, `guess` выглядит так: `5\n`. Символ `\n` представляет символ «новая строка» как результат нажатия <span class="keystroke">ввода</span>. Метод `trim` исключает `\n`, в результате получаем `5`.

Метод [`parse` у строк ](../std/primitive.str.html#method.parse) разбирает строку в число некоторого типа. Поскольку этот метод может анализировать различные типы чисел, то нужно указать точный тип числа, который мы хотим получить с помощью `let guess: u32`. Двоеточие (`:`) после `guess`, говорит Rust что мы аннотировали тип переменной. Rust имеет несколько встроенных числовых типов; здесь вы видите `u32` являющийся 32-битным без знаковым целым числом. Это хороший выбор по умолчанию для небольшого положительного числа. Вы узнаете о других типах чисел в главе 3. Кроме того, аннотация `u32` в этом примере программы и сравнение с `secret_number` означает, что Rust
выведет что `secret_number` должен иметь тип `u32`. Так что теперь сравнение будет между двумя значениями одного типа!

Вызов метода `parse` может легко вызвать ошибку. Если, например, строка содержит `A👍%` , то нет никакого способа преобразовать его в число. Так как вызов `read_line` может дать сбой, то метод `parse` возвращает тип `Result`, так же как `read_line` метод (обсуждался ранее в {a5}“Обработка потенциального сбоя с помощью `Result` типа"{/a5}). Мы будем обрабатывать этот `Result` снова, используя метод `expect`. Если `parse` возвращает вариант `Err` перечисления `Result`, потому что он не может создать число из строки, то вызов `expect` приведет к сбою игры и распечатает сообщение, которое мы передали в него. Если `parse` сможет успешно преобразовать строку в число, он вернет перечисления `Result` с вариантом `Ok`, а `expect` вернёт
число, которое мы хотим от значения `Ok`.

Let’s run the program now!

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 0.43 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 58
Please input your guess.
  76
You guessed: 76
Too big!
```

Nice! Even though spaces were added before the guess, the program still figured
out that the user guessed 76. Run the program a few times to verify the
different behavior with different kinds of input: guess the number correctly,
guess a number that is too high, and guess a number that is too low.

We have most of the game working now, but the user can make only one guess.
Let’s change that by adding a loop!

## Allowing Multiple Guesses with Looping

The `loop` keyword creates an infinite loop. We’ll add that now to give users
more chances at guessing the number:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
// --snip--

    println!("The secret number is: {}", secret_number);

    loop {
        println!("Please input your guess.");

        // --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

As you can see, we’ve moved everything into a loop from the guess input prompt
onward. Be sure to indent the lines inside the loop another four spaces each
and run the program again. Notice that there is a new problem because the
program is doing exactly what we told it to do: ask for another guess forever!
It doesn’t seem like the user can quit!

The user could always interrupt the program by using the keyboard shortcut <span class="keystroke">ctrl-c</span>. But there’s another way to escape this
insatiable monster, as mentioned in the `parse` discussion in [“Comparing the
Guess to the Secret Number”](#comparing-the-guess-to-the-secret-number)<comment>: if the user enters a non-number answer, the program will crash. The
user can take advantage of that in order to quit, as shown here:</comment>

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
    Finished dev [unoptimized + debuginfo] target(s) in 1.50 secs
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 59
Please input your guess.
45
You guessed: 45
Too small!
Please input your guess.
60
You guessed: 60
Too big!
Please input your guess.
59
You guessed: 59
You win!
Please input your guess.
quit
thread 'main' panicked at 'Please type a number!: ParseIntError { kind: InvalidDigit }', src/libcore/result.rs:785
note: Run with `RUST_BACKTRACE=1` for a backtrace.
error: Process didn't exit successfully: `target/debug/guess` (exit code: 101)
```

Typing `quit` actually quits the game, but so will any other non-number input.
However, this is suboptimal to say the least. We want the game to automatically
stop when the correct number is guessed.

### Quitting After a Correct Guess

Let’s program the game to quit when the user wins by adding a `break` statement:

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
// --snip--

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

Adding the `break` line after `You win!` makes the program exit the loop when
the user guesses the secret number correctly. Exiting the loop also means
exiting the program, because the loop is the last part of `main`.

### Handling Invalid Input

Для дальнейшего улучшения поведения игры вместо аварийного завершения программы при вводе пользователем не числовых значений, давайте заставим игру игнорировать не число, поэтому пользователь может продолжать угадывать. Можно сделать это, изменив строку, где `guess` преобразуется из `String` в `u32`, как показано в листинге 2-5.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
// --snip--

io::stdin().read_line(&mut guess)
    .expect("Failed to read line");

let guess: u32 = match guess.trim().parse() {
    Ok(num) => num,
    Err(_) => continue,
};

println!("You guessed: {}", guess);

// --snip--
```

<span class="caption">Listing 2-5: Ignoring a non-number guess and asking for
another guess instead of crashing the program</span>

Switching from an `expect` call to a `match` expression is how you generally
move from crashing on an error to handling the error. Remember that `parse`
returns a `Result` type and `Result` is an enum that has the variants `Ok` or
`Err`. We’re using a `match` expression here, as we did with the `Ordering`
result of the `cmp` method.

If `parse` is able to successfully turn the string into a number, it will
return an `Ok` value that contains the resulting number. That `Ok` value will
match the first arm’s pattern, and the `match` expression will just return the
`num` value that `parse` produced and put inside the `Ok` value. That number
will end up right where we want it in the new `guess` variable we’re creating.

If `parse` is *not* able to turn the string into a number, it will return an
`Err` value that contains more information about the error. The `Err` value
does not match the `Ok(num)` pattern in the first `match` arm, but it does
match the `Err(_)` pattern in the second arm. The underscore, `_`, is a
catchall value; in this example, we’re saying we want to match all `Err`
values, no matter what information they have inside them. So the program will
execute the second arm’s code, `continue`, which tells the program to go to the
next iteration of the `loop` and ask for another guess. So, effectively, the
program ignores all errors that `parse` might encounter!

Now everything in the program should work as expected. Let’s try it:

```text
$ cargo run
   Compiling guessing_game v0.1.0 (file:///projects/guessing_game)
     Running `target/debug/guessing_game`
Guess the number!
The secret number is: 61
Please input your guess.
10
You guessed: 10
Too small!
Please input your guess.
99
You guessed: 99
Too big!
Please input your guess.
foo
Please input your guess.
61
You guessed: 61
You win!
```

Awesome! With one tiny final tweak, we will finish the guessing game. Recall
that the program is still printing the secret number. That worked well for
testing, but it ruins the game. Let’s delete the `println!` that outputs the
secret number. Listing 2-6 shows the final code.

<span class="filename">Filename: src/main.rs</span>

```rust,ignore
use std::io;
use std::cmp::Ordering;
use rand::Rng;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin().read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

<span class="caption">Listing 2-6: Complete guessing game code</span>

## Summary

At this point, you’ve successfully built the guessing game. Congratulations!

This project was a hands-on way to introduce you to many new Rust concepts:
`let`, `match`, methods, associated functions, the use of external crates, and
more. In the next few chapters, you’ll learn about these concepts in more
detail. Chapter 3 covers concepts that most programming languages have, such as
variables, data types, and functions, and shows how to use them in Rust.
Chapter 4 explores ownership, a feature that makes Rust different from other
languages. Chapter 5 discusses structs and method syntax, and Chapter 6
explains how enums work.
