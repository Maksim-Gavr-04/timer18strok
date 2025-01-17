# Таймер обратного отсчета в 18 строк кода JavaScript.

Бывает, что вам для чего-то нужен **таймер обратного отсчета**, в интернете есть много решений, однако они либо очень громоздкие, либо имеют зависимости от других библиотек. Сегодня мы рассмотрим, **как сделать таймер обратного отсчета на JavaScript в 18 строк кода**.

## План

*   Установить правильную дату окончания
*   Высчитать оставшееся время
*   Привести дату к удобному формату
*   Вывести данные таймера, как многоразовый объект
*   Отобразить часы на странице и остановить их, когда они достигнут нуля

## Устанавливаем правильную дату окончания

Во-первых, вам нужно установить правильную дату окончания. Это будет строка в любом из форматов, которые понимает `Date.parse()` метод. К примеру:

ISO 8601

```js
var deadline = '2015-12-31';
```

Короткий формат

```js
var deadline = '31/12/2015';
```


Или длинный формат

```js
var deadline = 'December 31 2015';
```

Каждый из этих форматов позволяет вам установить точное время(в часах, минутах, секундах) и временную зону. Например:

```js
var deadline = 'December 31 2015 23:59:59 GMT+02:00';
```

## Высчитываем оставшееся время

Следующий шаг - высчитать оставшееся время. Чтобы это сделать, нам нужно написать функцию, которая будет брать строку с временем окончания и считать разницу между этим временем и текущим. Вот как это выглядит:

```js
function getTimeRemaining(endtime){  
  var t = Date.parse(endtime) - Date.parse(new Date());  
  var seconds = Math.floor( (t/1000) % 60 );  
  var minutes = Math.floor( (t/1000/60) % 60 );  
  var hours = Math.floor( (t/(1000*60*60)) % 24 );  
  var days = Math.floor( t/(1000*60*60*24) );  
  return {  
   'total': t,  
   'days': days,  
   'hours': hours,  
   'minutes': minutes,  
   'seconds': seconds  
  };  
}
```

Для начала мы создаем переменную `t`, чтобы хранить оставшееся время. `Date.parse()` метод встроен в **JavaScript** и позволяет сконвертировать строку со временем в значение в миллисекундах. Это позволит нам вычитать одно время от другого и получать разницу между ними.

```js
var t = Date.parse(endtime) - Date.parse(new Date());
```

## Приводим дату к удобному формату

Теперь мы хотим перевести миллисекунды в дни, часы, минуты и секунды. Давайте использовать секунды как пример:

```js
var seconds = Math.floor( (t/1000) % 60 );
```

Разберемся, что здесь происходит.

*   Делим миллисекунды на 1000, чтобы перевести их в секунды
*   Делим общее число секунд на 60 и сохраняем остаток - вам не нужны все секунды, только те, что остались после того, как минуты были подсчитаны
*   Округлите вниз до ближайшего целого значения, потому что вам нужны полные секунды, а не их фракции

Повторите эту логику, чтобы сконвертировать миллисекунды в минуты, часы и дни.

## Выводим данные таймера, как многоразовый объект

Когда часы, минуты и секунды готовы, нам нужно вернуть их как многоразовый объект.

```js
return {  
  'total': t,  
  'days': days,  
  'hours': hours,  
  'minutes': minutes,  
  'seconds': seconds  
};
```

Этот объект позволяет вам вызывать вашу функцию и получать любое из вычисленных значений. Вот пример, как вы можете получить оставшиеся минуты:

```js
getTimeRemaining(deadline).minutes
```

Удобно, правда?

## Отображаем часы на странице и останавливаем их, когда они достигнут нуля

Сейчас у нас есть функция, которая возвращает нам оставшиеся дни, часы, минуты и секунды. Мы можем строить наш таймер. Во-первых, создайте следующую **html** структуру для часов:

```html
<div id="clockdiv"></div>
```

Затем напишите функцию, которая будет отображать данные внутри нашего **div'а**:

```js
function initializeClock(id, endtime){  
  var clock = document.getElementById(id);  
  var timeinterval = setInterval(function(){  
   var t = getTimeRemaining(endtime);  
   clock.innerHTML = 'days: ' + t.days + '  
' +  
    'hours: '+ t.hours + '  
' +  
    'minutes: ' + t.minutes + '  
' +  
    'seconds: ' + t.seconds;  
   if(t.total<=0){  
    clearInterval(timeinterval);  
   }  
  },1000);  
}
```

Эта функция принимает два параметра: `id` элемента, который будет содержать наши часы, и конечное время счетчика. Внутри функции мы объявим переменную `clock` и будем использовать ее, чтобы хранить ссылку на наш блок с часами, так что нам не нужно запрашивать **DOM**.

Дальше мы будем использовать `setInterval`, чтобы запускать анонимную функцию каждую секунду, которая будет делать следующее:

*   Высчитывать оставшееся время
*   Выводить оставшееся время в наш `div`
*   Если оставшееся время `= 0`, останавливать часы

Единственное, что осталось, запустить часы следующим образом:

```js
initializeClock('clockdiv', deadline);
```

Поздравляю! Теперь у вас есть **простой таймер обратного отсчета всего в 18 строк JavaScript кода**.

## Подготавливаем наши часы для отображения

До стилизации нам будет нужно немного усовершенствовать некоторые вещи.

*   Убрать начальную задержку, чтобы таймер показывался незамедлительно
*   Сделать скрипт часов более эффективным, чтобы не приходилось непрерывно перестраивать все часы
*   Добавить нули по желанию

## Убираем начальную задержку

В часах мы используем `setInterval`, чтобы обновлять отображение каждую секунду. Чаще всего это нормально, кроме начала, где присутствует 1с задержка. Чтобы это исправить, нам нужно обновлять часы один раз до того, как `setInterval` запускается.

Чтобы это сделать, давайте переместим анонимную функцию, которую мы передаем в `setInterval` (ту, которая обновляет часы каждую секунду) в собственную отдельную функцию, которую назовем `updateClock`. Вызовите эту функцию однажды вне `setInterval` и затем вызовите ее снова внутри `setInterval`. Таким образом, часы будут показываться без задержки.

В вашем **JavaScript** замените это:

```js
var timeinterval = setInterval(function(){ ... },1000);
```

На это:

```js
function updateClock(){  
  var t = getTimeRemaining(endtime);  
  clock.innerHTML = 'days: ' + t.days + '  
' +  
   'hours: '+ t.hours + '  
' +  
   'minutes: ' + t.minutes + '  
' +  
   'seconds: ' + t.seconds;  
  if(t.total<=0){  
   clearInterval(timeinterval);  
  }  
}  

updateClock(); // запустите функцию один раз, чтобы избежать задержки  
var timeinterval = setInterval(updateClock,1000);
```

## Делаем скрипт более эффективным

Чтобы сделать скрипт более эффективным, нам нужно обновлять не все часы, а только цифры. Для этого поместим каждое число в тег `span` и будем обновлять только этот контент.

Вот **html**:

```html
<div id="clockdiv">  
  Days: <span class="days"></span><br>  
  Hours: <span class="hours"></span><br>  
  Minutes: <span class="minutes"></span><br>  
  Seconds: <span class="seconds"></span>  
</div>
```

Теперь сделаем ссылку на эти элементы. Добавьте следующий код прямо после определения переменной `clock`.

```js
var daysSpan = clock.querySelector('.days');  
var hoursSpan = clock.querySelector('.hours');  
var minutesSpan = clock.querySelector('.minutes');  
var secondsSpan = clock.querySelector('.seconds');
```

Дальше нам нужно изменить функцию `updateClock`, чтобы обновить только числа, а не все часы. Новый код будет выглядеть так:

```js
function updateClock(){  
  var t = getTimeRemaining(endtime);  

  daysSpan.innerHTML = t.days;  
  hoursSpan.innerHTML = t.hours;  
  minutesSpan.innerHTML = t.minutes;  
  secondsSpan.innerHTML = t.seconds;  

  ...  
}
```

## Добавляем ведущие нули

Если вам нужны ведующие нули, вы можете заменить код такого вида:

```js
secondsSpan.innerHTML = t.seconds;
```

На такой:

```js
secondsSpan.innerHTML = ('0' + t.seconds).slice(-2);
```

## Заключение

Мы рассмотрели, **как сделать простой таймер обратного отсчета на JavaScript**. Все, что вам осталось, это добавить стили.

Источник: http://www.sitepoint.com/build-javascript-countdown-timer-no-dependencies/
Перевод взят отсюда: https://myrusakov.ru/js-countdown-timer.html

Оригинал: https://gist.github.com/nptit/89e4aa0cd1f5b2b9a258ac0c4e202e02