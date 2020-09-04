# vue-calendar

<pre style="background-color: #fff;">
                                           <h1>Guide</h1>
    Компонент не использует сторонних библиотек. Для построения календаря принимает два пропса:
объект data по типу приведенного ниже (может быть пустым) и значение showSelected типа boolean
отвечающее за отображение подсветки выбранной даты (не обязательно). При выборе даты компонент
инициирует событие DateSelected и вместе с ним возвращает выбранную дату.

<img src="https://raw.githubusercontent.com/AfanasevAV/vue-calendar/master/public/favicon.png" alt="calendar" style="max-width:100%;float: right;">

<h2>Использование компонента в родительском шаблоне:</h2>

&lt;calendar v-bind:data="data" v-bind:showSelected="true" 
@DateSelected="setDate"/&gt;
&lt;calendar :data="{}"/&gt;

{
    data: {
        date: "2020-04-01",
        show_years: false,
        show_buttons: false,
        mark_weekend: false,
        current_month: false,
        event_init: false,
        mark_days: [
            {date: "2020-04-04", title: "Выходной день."},
            {date: "2020-04-05", title: "Выходной день."},
            {date: "2020-04-11", title: "Выходной день."},
            {date: "2020-04-12", title: "Выходной день."},
            {date: "2020-04-18", title: "Выходной день."},
            {date: "2020-04-19", title: "Выходной день."},
            {date: "2020-04-25", title: "Выходной день."},
            {date: "2020-04-26", title: "Выходной день."},
            {
                date: "2020-04-30",
                title: "Время работы сокращено на 1 час",
                style: "background: rgb(254, 239, 123);"
            },
        ]
    },
},

<h2>Запуск проекта:</h2>

1) git clone https://github.com/AfanasevAV/vue-calendar.git
2) cd vue-calendar
3) npm i
4) npm run serve
</pre>
