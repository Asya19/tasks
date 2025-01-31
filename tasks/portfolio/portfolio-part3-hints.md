## Советы по выполнению задания Portfolio#3-Добавление функционала

- [Описание и требования задания Portfolio#3-Добавление функционала](portfolio-part3.md)
- Советы не являются частью задания. Следуйте советам только в той мере, в которой они могут вам помочь.

1. Смена изображений в секции Portfolio
   - [Методы поиска элементов](#методы-поиска-элементов)
   - [Шаблонные строки](#шаблонные-строки)
   - [Делегирование](#делегирование)
   - [data-атрибуты](#data-атрибуты)
   - [Кеширование изображений](#кеширование-изображений)
2. Перевод страницы на два языка
   - [](#)
   - [](#)
   - [](#)
3. Переключение светлой и тёмной темы
   - [](#)
   - [](#)
   - [](#)

### Смена изображений в секции Portfolio

JavaScript очень терпим к качеству кода.  
Можно написать совсем простой даже примитивный код, и он будет работать.  
Работоспособный код это достойная уважения цель, но когда она достигнута, можно попробовать написанный код улучшить.

Мы сделаем так: сначала напишем максимально простой, но работоспособный код, а потом посмотрим как его можно улучшить.  
В процессе улучшения (рефакторинга) кода рассмотрим такие вопросы как
- методы поиска элементов
- шаблонные строки
- делегирование событий
- data-атрибуты

Рассмотрим достаточно подробно для того, чтобы вы их хорошо усвоили, и в дальнейшем больше к этим темам не возвращаться.

В секции Portfolio в файле `index.html` есть кнопка и изображение  
```html
<button class="portfolio-btn">Winter</button>

<img src="./assets/img/autumn/1.jpg" alt="portfolio-image" class="portfolio-image">
```  

Посмотрим, как можно изменить изображение, кликом по кнопке.  

Нам нужно:  
1) найти кнопку
2) найти изображение
3) при клике по кнопке изменить `src` изображения, заменив `autumn` на `winter`.  

Код этого волшебства будет выглядеть следующим образом:  
```js
const portfolioBtn = document.querySelector('.portfolio-btn');
const portfolioImage = document.querySelector('.portfolio-image');

portfolioBtn.addEventListener('click', () => {
   portfolioImage.src = "./assets/img/winter/1.jpg"
});
```  
Собственно, всё.  
Правда у нас четыре кнопки и шесть картинок, но, присвоив каждой кнопке и каждой картинке уникальный класс, и повторив этот фрагмент кода 4х6=24 раза, мы сможем добиться поставленной цели.  
Это если бы цель была просто выполнить задание.  
Но так как цель научиться новому, займёмся рефакторингом кода.

### Методы поиска элементов

Для поиска элементов в 90% или даже 99% случаев используются методы `.querySelector()` и `.querySelectorAll()`. Первый позволяет найти один элемент на странице, второй - все, если элементов несколько.

Сравнительно недавно элементы в js искали по `id`. Для стилизации элементов использовали классы, а если предполагалось эти элементы использовать в js, им добавляли ещё и `id`. Поиск элементов по `id` даёт небольшое преимущество в скорости работы. Но использование `id` противоречит методологии БЭМ. Так как уникальный `id` на странице может быть только один, код, написанный с использованием `id` не получится оформить в отдельный модуль и использовать в разных проектах: ведь если в другом проекте уже есть элемент с таким же `id`, код сломается и работать не будет. 

Поэтому сейчас элементы принято искать по их классам. Нет необходимости присваивать элементам одни классы для стилизации, другие для js. Мы добавляем элементу класс, по этому классу элемент стилизуем, и по нему же находим на странице.

**Пример**

Найдём на странице кнопку с классом `portfolio-btn` и запишем результат поиска в переменную `portfolioBtn`  
```js
const portfolioBtn = document.querySelector('.portfolio-btn');
```  

Найдём на странице все изображения с классом `portfolio-image` и запишем результат поиска в переменную `portfolioImages`:  
```js
const portfolioImages = document.querySelectorAll('.portfolio-image');
```  

**Обратите внимание:**

- все переменные, соответствующие элементам, которые есть в коде страницы, объявляются при помощи ключевого слова `const`
- также `const` используется для объявления переменных, которые мы не планируем менять
- ключевое слово `let` используется для объявления переменных, значения которых будут изменяться
- названия переменных - существительные, названия функций - глаголы
- в css используем kebab-case: все буквы маленькие, слова разделяются дефисом
- в js используем camelCase: первая буква всегда маленькая, далее каждое слово начинается с большой буквы

Разница между методами `.querySelector()` и `.querySelectorAll()` не только в том, что первый находит один элемент, а второй несколько.  
- метод `querySelector()` находит отдельный элемент, с которым можно что-то делать
- метод `querySelectorAll()` находит коллекцию элементов NodeList, которую нужно перебирать при помощи метода `forEach()`.

Метод `querySelectorAll()` позволил найти на странице все изображения с классом `portfolio-image`, теперь мы можем изменить `src` у всех картинок сразу:  
```js
portfolioImages.forEach((img, index) => img.src = `./assets/img/winter/${index + 1}.jpg`);
```  
Перебор картинок методом `.forEach()` выглядит чуть сложнее, чем изменение `src` у каждой картинки отдельно, но благодаря ему мы смогли сократить количество кода в шесть раз. А если учесть, что элементов, которые нужно изменить, на странице может быть много, использование этого метода безусловно полезно и с ним очень желательно разобраться.

### Шаблонные строки

Шаблонные строки используются вместо обычных строк в тех случаях, если внутри строки нам необходимо использовать переменную.  
Это действительно очень удобно.  
В предыдущем примере нам необходимо было указать `src` каждого изображения с учётом его индекса. Первое изображение `"./assets/img/winter/1.jpg"`, второе - `"./assets/img/winter/2.jpg"`, шестое - `"./assets/img/winter/6.jpg"`. 

С использованием обычных строк, код выглядел бы так:  
```js
img.src = "./assets/img/winter/" + (index + 1) + ".jpg"
```  
(index + 1) используем потому что `index` начинается с нуля, а нумерация картинок в папке проекта с 1.

В шаблонной строке вместо одинарных или двойных кавычек используются косые кавычки (на клавиатуре находятся на одной клавише с буквой "ё"). Чтобы подставить в шаблонную строку переменную или выражение, заключаем их в фигурные скобки, перед которыми ставим знак доллара - "$".

Шаблонные строки широко используются в современном js, знать о них и использовать их в своём коде, безусловно, полезно.

### Делегирование

Делегирование мы уже использовали, когда скрывали адаптивное меню при клике по любой из ссылок во [второй части задания](portfolio-part2-hints.md#adaptive-menu)  

Так как делегирование - важный и нужный приём разработки, повторим ещё раз этапы работы с ним.  
Чтобы не назначать отдельный слушатель каждому элементу с одинаковым или схожим функционалу:
1. Находим их ближайшего общего родителя
2. Проверяем, что событие произошло на нужном элементе
3. Выполняем функцию

Ближайший общий родитель кнопок с классом `portfolio-btn` - их общий контейнер `portfolio-btns`. Находим его на странице и присваиваем переменной `portfolioBtns`  
```js
const portfolioBtns = document.querySelector('.portfolio-btns');
```  
В функцию `changeImage()`, которую будем выполнять при клике по находящимся внутри `portfolioBtns` кнопкам, добавляем проверку, что целевой элемент `event.target` имеет класс `portfolio-btn`, то есть является кнопкой.  
```js
function changeImage(event) {
  if(event.target.classList.contains('portfolio-btn')) {
    // здесь код функции, меняющей src изображений
  }
}
```  

### data-атрибуты

Код функции `changeImage()`, которая выполняется при кликах по кнопкам, хоть и похожий, но разный. При клике по одной кнопке должны появиться весенние изображения, при клике по другой - зимние.  
Делегирование, которое убсуждали выше, позволяет определить, что пользователь кликнул по кнопке. Но как узнать что это была за кнопка и какие изображения нужно отобразить при клике по ней?

Для распознавания элементов в html-коде страницы используются data-атрибуты.  
Название и значение data-атрибутов разработчик определяет сам. Единственное условие: название data-атрибута всегда начинается с префикса data-, дальше идёт любое выбранное вами слово. Например:  
```html
<div class="portfolio-btns">
  <button class="portfolio-btn" data-season="winter">Winter</button>
  <button class="portfolio-btn" data-season="spring">Spring</button>        
  <button class="portfolio-btn" data-season="summer">Summer</button>
  <button class="portfolio-btn" data-season="autumn">Autumn</button>
</div>
```  
Получить data-атрибут элемента можно при помощи `dataset` и ключа - слова, которое в названии атрибута идёт после префикса data-. Для первой кнопки `dataset.season = "winter"`, для второй `dataset.season = "spring"` и так далее.  

В коде функции `changeImage()`, которая выполняется при клике по кнопке, необходимо определить значение `dataset.season` этой кнопки и подставить его в шаблонную строку, определяющую `src` изображения. Благодаря этому при клике по кнопке с data-атрибутом "winter" на странице будут отображаться картинки из папки "winter", а если data-атрибут кнопки "summer", то и картинки будут отображаться из папки "summer".

### Кеширование изображений

После того, как изображение было загружено в браузер, оно добавится в кеш браузера и при следующем использовании будет загружаться намного быстрее.  
В обычной ситуации изображения кешируются, когда их открывает пользователь, но можно кешировать изображения средствами js.  
Всё, что для этого необходимо: сказать браузеру, что у нас есть такая картинка.  
Для этого создаём новое изображение и указываем его src. Так выглядит функция кеширования изображений из папки "summer"  
```js
function preloadSummerImages() {
  for(let i = 1; i <= 6; i++) {
    const img = new Image();
    img.src = `./assets/img/summer/${i}.jpg`;
  }
}
preloadSummerImages();
```  
Самостоятельно напишите функцию `preloadImages()` для кеширования изображений из всех папок с временами года.  
```js
const seasons = ['winter', 'spring', 'summer', 'autumn'];
```  
Для перебора элементов массива понадобится метод `.forEach()`, а в шаблонную строку необходимо будет подставить не только переменную с номером изображения, но и переменную с названием папки.
