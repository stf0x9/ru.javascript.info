archive:
  ref: null

---

# Переменные: let и const

В ES-2015 предусмотрены новые способы объявления переменных: через `let` и `const` вместо `var`.

Например:
```js
let a = 5;
```

## let

У объявлений переменной через `let` есть три основных отличия от `var`:

1. **Область видимости переменной `let` -- блок `{...}`.**

    Как мы помним, переменная, объявленная через `var`, видна везде в функции.

    Переменная, объявленная через `let`, видна только в рамках блока `{...}`, в котором объявлена.

    Это, в частности, влияет на объявления внутри `if`, `while` или `for`.

    Например, переменная через `var`:

    ```js run
    var apples = 5;

    if (true) {
      var apples = 10;

      alert(apples); // 10 (внутри блока)
    }

    alert(apples); // 10 (снаружи блока то же самое)
    ```

    В примере выше `apples` -- одна переменная на весь код, которая модифицируется в `if`.

    То же самое с `let` будет работать по-другому:

    ```js run
    let apples = 5; // (*)

    if (true) {
      let apples = 10;

      alert(apples); // 10 (внутри блока)
    }

    *!*
    alert(apples); // 5 (снаружи блока значение не изменилось)
    */!*
    ```

    Здесь, фактически, две независимые переменные `apples`, одна -- глобальная, вторая -- в блоке `if`.

    Заметим, что если объявление `let apples` в первой строке `(*)` удалить, то в последнем `alert` будет ошибка: переменная не определена:

    ```js run
    if (true) {
      let apples = 10;

      alert(apples); // 10 (внутри блока)
    }

    *!*
    alert(apples); // ошибка!
    */!*
    ```

    Это потому что переменная `let` всегда видна именно в том блоке, где объявлена, и не более.
2. **Переменная `let` видна только после объявления.**

    Как мы помним, переменные `var` существуют и до объявления. Они равны `undefined`:

    ```js run
    alert(a); // undefined

    var a = 5;
    ```

    С переменными `let` всё проще. До объявления их вообще нет.

    Такой доступ приведёт к ошибке:
    ```js run
    *!*
    alert(a); // ошибка, нет такой переменной
    */!*

    let a = 5;
    ```

    Заметим также, что переменные `let` нельзя повторно объявлять. То есть, такой код выведет ошибку:

    ```js run
    let x;
    let x; // ошибка: переменная x уже объявлена
    ```

    Это -- хоть и выглядит ограничением по сравнению с `var`, но на самом деле проблем не создаёт. Например, два таких цикла совсем не конфликтуют:
    ```js run
    // каждый цикл имеет свою переменную i
    for(let i = 0; i<10; i++) { /* … */ }
    for(let i = 0; i<10; i++) { /* … */ }

    alert( i ); // ошибка: глобальной i нет
    ```

    При объявлении внутри цикла переменная `i` будет видна только в блоке цикла. Она не видна снаружи, поэтому будет ошибка в последнем `alert`.
3. **При использовании в цикле, для каждой итерации создаётся своя переменная.**

    Переменная `var` -- одна на все итерации цикла и видна даже после цикла:

    ```js run
    for(var i=0; i<10; i++) { /* … */ }

    alert(i); // 10
    ```

    С переменной `let` -- всё по-другому.

    Каждому повторению цикла соответствует своя независимая переменная `let`. Если внутри цикла есть вложенные объявления функций, то в замыкании каждой будет та переменная, которая была при соответствующей итерации.

    Это позволяет легко решить классическую проблему с замыканиями, описанную в задаче <info:task/make-army>.

    ```js run
    function makeArmy() {

      let shooters = [];

      for (*!*let*/!* i = 0; i < 10; i++) {
        shooters.push(function() {
          alert( i ); // выводит свой номер
        });
      }

      return shooters;
    }

    var army = makeArmy();

    army[0](); // 0
    army[5](); // 5
    ```

    Если бы объявление было `var i`, то была бы одна переменная `i` на всю функцию, и вызовы в последних строках выводили бы `10` (подробнее -- см. задачу <info:task/make-army>).

    А выше объявление `let i` создаёт для каждого повторения блока в цикле свою переменную, которую функция и получает из замыкания в последних строках.

## const

Объявление `const` задаёт константу, то есть переменную, которую нельзя менять:

```js run
const apple = 5;
apple = 10; // ошибка
```

В остальном объявление `const` полностью аналогично `let`.

Заметим, что если в константу присвоен объект, то от изменения защищена сама константа, но не свойства внутри неё:

```js 
const user = {
  name: "Вася"
};

user.name = "Петя"; // допустимо
user = 5; // нельзя, будет ошибка
```

То же самое верно, если константе присвоен массив или другое объектное значение. 

```smart header="константы и КОНСТАНТЫ"
Константы, которые жёстко заданы всегда, во время всей программы, обычно пишутся в верхнем регистре. Например: `const ORANGE = "#ffa500"`.

Большинство переменных -- константы в другом смысле: они не меняются после присвоения. Но при разных запусках функции это значение может быть разным. Для таких переменных можно использовать `const` и обычные строчные буквы в имени.
```

## Итого

Переменные `let`:

- Видны только после объявления и только в текущем блоке.
- Нельзя переобъявлять (в том же блоке).
- При объявлении переменной в цикле `for(let …)` -- она видна только в этом цикле. Причём каждой итерации соответствует своя переменная `let`.

Переменная `const` -- это константа, в остальном -- как `let`.
