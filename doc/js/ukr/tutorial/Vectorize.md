# Векторизація рутин

Використання рутини <code>vectorize</code> для векторизації скалярних рутин.

### Векторизація рутини з налаштуваннями за замовчуванням

Для демонстрації можливості рутини `vectorize` розв'яжемо задачу.

Потрібно попарно просумувати числа з двох векторів, реалізація сумування скалярних величин виглядатиме так:

```js
function sum( a, b )
{
  return Number( a ) + Number( b );
}
console.log( sum( 1, 2 ) );
// 3
console.log( sum( [1, 2], [3, 4] ) );
// NaN
```

Код рутини `sum` показує, що вона повертає суму двох чисел, а при передачі інших типів даних, в тому числі,
векторів даних - `NaN`. Рутина не містить обмежень кількості аргументів та їх типу.

Виконаємо векторизацію рутини `sum` двома способами.

Перший спосіб - ручне написання векторизації.

Рутина `sum_v1` використовує код рутини `sum` при додаванні елементів з однаковими індексами - це приклад простої
реалізації векторизації. Однак, для її написання потрібно щонайменше чотири рядки.

```js
// manually written vectorized routine
function sum_v1( a, b )
{
  let result = [];
  for( let i = 0; i < a.length; i++ )
  result[ i ] = sum( a[ i ], b[ i ] );
  return result;
}

// vectors
let a = [ 1, 3, 4, 6, 9 ];
let b = [ 4, 1, 0, 7, 3 ];

console.log( sum_v1( a, b ) );
// [ 5, 4, 4, 13, 12 ]
```
Другий спосіб - автоматично, з допомогою рутини `vectorize`, що займає лише один рядок коду.

Для його реалізації змінній `sum_v2` присвоєно результат виконання рутини `vectorize`.

```js
let _ = require( 'wTools' );

// automatically vectorized routine by vectorize(func, n)
let sum_v2 = _.vectorize( sum, 2 );

// vectors
let a = [ 1, 3, 4, 6, 9 ];
let b = [ 4, 1, 0, 7, 3 ];

console.log( sum_v2( a, b ) );
// [ 5, 4, 4, 13, 12 ]
```

Рутина `vectorize` приймає два аргументи:
`func` - скалярна рутина, яку необхідно векторизувати,
`n` - кількість аргументів, що прийматиме векторизована рутина.

Тепер спробуємо передати рутинам скалярні дані.

```js
a = 1;
b = 2;

console.log( sum_v1( a, b ) );
// []
console.log( sum_v2( a, b ) );
// 3
```

Рутина `sum_v1` повернула порожній масив, оскільки передані дані не є векторами, а рутина `sum_v2` повернула скалярний
результат. Тобто, рутини, векторизовані з допомогою `vectorize` можуть працювати зі скалярами та векторами.

Тепер передамо рутинам вектори `a` i `b`, які мають різну довжину.

```js
a = [ 1, 3, 4, 6, 9 ];
b = [ 4, 1, 0, 7 ];

console.log( sum_v1( a, b ) );
// [ 5, 4, 4, 13, NaN ]
console.log( sum_v2( a, b ) );
/*
------------------------------- unhandled errorr ------------------------------->

 * Application
Current path : /some/path/to/file
Exec path : /usr/bin/node /some/path/to/file/Sum.js

 = Message
Vector should have 5 elements, but have 4

... error log
------------------------------- unhandled errorr -------------------------------<
*/
```

Рутина `sum_v1`, просумувала наявні елементи та вивела масив.
У векторі `b` відсутній п'ятий елемент, тому в поверненому масиві останній елемент має значення `NaN`.\
При виконанні операції над декількома векторами їх довжина має бути однаковою, тому рутина `sum_v1`
видала некоректний результат. Очевидно, що програмісту при створенні рутини `sum_v1` довелось би встановити умову
еквівалентності довжин векторів. Ця умова автоматично додана рутиною `vectorize` в рутину `sum_v2`. Тому, слідом за
виводом рутини `sum_v1` викинуто помилку. Повідомлення говорить про різну довжину векторів

Ще однією позитивною стороною використання рутини `vectorize` є можливість встановлення кількості аргументів,
що мають передаватись. В рутині `sum_v2` воно встановлене числом `2`.

```js
console.log( sum_v1( a, a, a ) );
// [ 2, 6, 8, 12, 18 ]
console.log( sum_v2( b, b, b ) );
/*
------------------------------- unhandled errorr ------------------------------->

 * Application
Current path : /some/path/to/file
Exec path : /usr/bin/node /some/path/to/file/Sum.js

 = Message
Expects 2 arguments, but got 3

... error log
------------------------------- unhandled errorr -------------------------------<
*/
```
Рутина `sum_v1` успішно виконала операцію з першими двома аргументами, а третій проігнорувала. Рутина `sum_v2` викинула помилку, оскільки кількість переданих аргументів відрізняється від очікуваної.

Приведений приклад розкриває базові можливості рутини `vectorize`, поглиблене розуміння можна отримати прочитавши туторіал
по використанню [опцій рутини vectorize](VectorizeOptions.md).

### Підсумок

- Рутина `vectorize` дозволяє автоматично створювати векторизовані рутини зі скалярних.
- Рутина, котра створена з допомогою `vectorize` може виконувати операції зі скалярними величинами так само, як і скалярна рутина.
- Рутина `vectorize` може встановлювати кількість аргументів для векторизованої рутини.
- В векторизовані рутини додається перевірка переданих векторів на співрозмірність їх довжин.

[Повернутись до змісту](../README.md#туторіали)