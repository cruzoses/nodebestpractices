# Використовуйте Async-Await або promises для обробки асинхронних помилок

### Пояснення за один абзац

Зворотні виклики погано масштабуються, оскільки більшість програмістів з ними не знайомі. Вони змушують перевіряти помилки скрізь, мають справу з неприємним вкладенням коду та ускладнюють міркування про потік коду. Бібліотеки Promise, такі як [BlueBird](https://www.npmjs.com/package/bluebird), [async](https://www.npmjs.com/package/async) і Q (deprecated), містять стандартний стиль коду, використовуючи RETURN і THROW для керування потоком програми. Зокрема, вони підтримують улюблений стиль обробки помилок спроби-перехоплення, який дозволяє звільнити шлях основного коду від роботи з помилками в кожній функції
### Приклад коду – використання обіцянок для виявлення помилок

```javascript
return functionA()
  .then(functionB)
  .then(functionC)
  .then(functionD)
  .catch((err) => logger.error(err))
  .then(alwaysExecuteThisFunction)
```


### Приклад коду - використання async/await для виявлення помилок

```javascript
async function executeAsyncTask () {
  try {
    const valueA = await functionA();
    const valueB = await functionB(valueA);
    const valueC = await functionC(valueB);
    return await functionD(valueC);
  }
  catch (err) {
    logger.error(err);
  } finally {
    await alwaysExecuteThisFunction();
  }
}
```

### Приклад антишаблонного коду – обробка помилок стилю зворотного виклику

<details>
<summary><strong>Javascript</strong></summary>

```javascript
getData(someParameter, function(err, result) {
    if(err !== null) {
        // викликаєм коллбек функцію і передаємо помилку
        getMoreData(a, function(err, result) {
            if(err !== null) {
                // викликаєм коллбек функцію і передаємо помилку
                getMoreData(b, function(c) {
                    getMoreData(d, function(e) {
                        if(err !== null ) {
                            // ви зрозуміли ідею?
                        }
                    })
                });
            }
        });
    }
});
```
</details>

<details>
<summary><strong>Typescript</strong></summary>

```typescript
getData(someParameter, function(err: Error | null, resultA: ResultA) {
  if(err !== null) {
    // викликаєм коллбек функцію і передаємо помилку
    getMoreData(resultA, function(err: Error | null, resultB: ResultB) {
      if(err !== null) {
        // викликаєм коллбек функцію і передаємо помилку
        getMoreData(resultB, function(resultC: ResultC) {
          getMoreData(resultC, function(err: Error | null, d: ResultD) {
            if(err !== null) {
              // ви зрозуміли ідею?
            }
          })
        });
      }
    });
  }
});
```
</details>

### Цитата з блогу: "У нас проблеми з обіцянками (Promises)"

 З блогу pouchdb.com

 > ……І насправді зворотні виклики роблять щось ще зловісніше: вони позбавляють нас стека, який ми зазвичай сприймаємо як належне в мовах програмування. Написання коду без стека схоже на керування автомобілем без педалі гальма: ви не усвідомлюєте, наскільки сильно він вам потрібен, поки не потягнетеся до нього, а його немає. Суть обіцянок полягає в тому, щоб повернути нам основи мови, які ми втратили, коли перейшли на асинхронізацію: return, throw і стек. Але ви повинні знати, як правильно використовувати обіцянки, щоб скористатися ними.

### Цитата з блогу: «Метод обіцянок (Promises) набагато компактніший»

 З блогу gosquared.com

 > ………Метод обіцянок набагато компактніший, зрозуміліший і швидший для написання. Якщо в будь-якій із операцій виникає помилка чи виняткова ситуація, це обробляється єдиним обробником .catch(). Наявність цього єдиного місця для обробки всіх помилок означає, що вам не потрібно писати перевірку помилок для кожного етапу роботи.

### Цитата з блогу: "Promises є нативним ES6, можна використовувати з генераторами"

 З блогу StrongLoop

 > …Зворотні виклики мають погану історію обробки помилок. Обіцянки краще. Поєднайте вбудовану обробку помилок у Express із обіцянками та значно зменште ймовірність неперехопленого винятку. Promises є нативним ES6, їх можна використовувати з генераторами, а пропозиції ES7, такі як async/await, через компілятори, такі як Babel

### Цитата з блогу: "Усі звичайні конструкції керування потоком, до яких ви звикли, повністю зламані"

З блогу Benno’s

 > ……Однією з найкращих переваг асинхронного програмування, заснованого на зворотних викликах, є те, що в основному всі звичайні конструкції керування потоком, до яких ви звикли, повністю зламані. Однак найбільше порушень я вважаю обробкою винятків. Javascript надає досить знайому конструкцію try…catch для роботи з винятками. Проблема з винятками полягає в тому, що вони забезпечують чудовий спосіб швидкого скорочення помилок у стеку викликів, але в кінцевому підсумку стають абсолютно марними, якщо помилка виникає в іншому стеку...