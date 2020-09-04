//Author:"Афанасьев А.В."
//
//                                                          Guide
//          Компонент не использует сторонних библиотек. Для построения календаря принимает два пропса: объект data по типу
//      приведенного ниже (может быть пустым) и значение showSelected типа boolean отвечающее за отображение подсветки выбранной
//      даты (не обязательно). При выборе даты компонент инициирует событие DateSelected и вместе с ним возвращает выбранную дату.
//
// <calendar v-bind:data="data" v-bind:showSelected="true" @DateSelected="setDate"/>
// <calendar :data="{}"/>
//
// {
//     data: {
//         date: "2020-04-01",
//         show_years: false,
//         show_buttons: false,
//         mark_weekend: false,
//         current_month: false,
//         event_init: false,
//         mark_days: [
//             {date: "2020-04-04", title: "Выходной день."},
//             {date: "2020-04-05", title: "Выходной день."},
//             {date: "2020-04-11", title: "Выходной день."},
//             {date: "2020-04-12", title: "Выходной день."},
//             {date: "2020-04-18", title: "Выходной день."},
//             {date: "2020-04-19", title: "Выходной день."},
//             {date: "2020-04-25", title: "Выходной день."},
//             {date: "2020-04-26", title: "Выходной день."},
//             {
//                 date: "2020-04-30",
//                 title: "Время работы сокращено на 1 час",
//                 style: "background: rgb(254, 239, 123);"
//             },
//         ]
//     },
// },


<template>
    <div class="calendar">
        <table>
            <thead>
                <tr>
                    <th class="button" v-on:click="previus_month" v-if="show_buttons">‹</th>
                    <th :colspan="date_colspan">
                        <span>{{months[current_date.getMonth()]}}</span>
                        <span v-if="this.show_years"> {{current_date.getFullYear()}}</span>
                    </th>
                    <th class="button" v-on:click="next_month" v-if="show_buttons">›</th>
                </tr>
                <tr>
                    <th v-for="(day, i) in days" v-bind:key="i" v-bind:class="{WeekendName: name_days[i]}">{{day}}</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(week, i) in weeks" v-bind:key="i">
                    <td v-for="(day, i) in week.days" v-bind:key="i"
                        v-bind:class="{Active: (day.isActive && showSelected), Visible: day.isVisible, Weekend: day.isWeekend, Marked: day.isMarked}"
                        v-bind:style="day.MarkedStyle"
                        v-bind:title="day.MarkedTitle"
                        v-on:click="chose_date(day.number)">
                        <span>{{day.number}}</span>{{day.style}}
                    </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
    export default {
        name: 'calendar',
        props: [
            "data",
            "showSelected"
        ],
        data() {
            return {
                current_date: this.data.date ? new Date(this.data.date) : new Date(),
                days: this.data.days || ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
                months: this.data.months || ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"],
                mark_days: this.data.mark_days || [],
                mark_weekend: (typeof this.data.mark_weekend != "undefined") ? this.data.mark_weekend : true,
                show_buttons: (typeof this.data.show_buttons != "undefined") ? this.data.show_buttons : true,
                show_years: (typeof this.data.show_years != "undefined") ? this.data.show_years : true,
                event_init: (typeof this.data.event_init != "undefined") ? this.data.event_init : true,
                date_colspan: 5,
                weeks: [],
                name_days: [],
                set_mouth: "",
                set_year: "",
                last_day: 0,
                last_style: "",
            }
        },
        methods: {
            previus_month() {//Метод переход к предыдущему месяцу
                this.current_date = new Date(this.current_date.getFullYear(), this.current_date.getMonth() - 1, this.current_date.getDate());
                this.initial_month();
            },
            next_month() {//Метод переход к следющему месяцу
                this.current_date = new Date(this.current_date.getFullYear(), this.current_date.getMonth() + 1, this.current_date.getDate());
                this.initial_month();
            },
            initial_month() {
                //Получаем количество дней в месяца
                let max_day_month = 32 - new Date(this.current_date.getFullYear(), this.current_date.getMonth(), 32).getDate();
                //Получаем день недели первого числа месяца
                let day = new Date(this.current_date.getFullYear(), this.current_date.getMonth(), 0).getDay();
                //Заполняем массив месяц новыми данными
                this.weeks = [];
                let day_week = 0; //Переменная счетчик для разбиения по неделям
                let days = [];//Буфер дат текущей недели
                let daysIsWeekend;//Флаг - день считается праздничным
                let daysIsMarked;//Флаг - день отмечен
                let strCurDate;//Буфер преобразованной в строку даты
                let bufDate;//Буфер используемый для преобразования даты в строку
                let MarkedStyle;//Буфер стиля отмеченного дня
                let MarkedTitle;//Буфер всплывающей подсказки

                for (let i = 0; i < day; i++) {//Пустые блоки
                    days.push({number: "", isActive: false, isVisible: false, isWeekend: false, isMarked: false});
                    day_week++;
                }
                for (let i = 1; i <= max_day_month; i++) {//Блоки с датой
                    if (day_week > 6) {
                        this.weeks.push({days});
                        days = [];
                        day_week = 0;
                    }
                    daysIsWeekend = (day_week >= 5 && this.mark_weekend) ? true : false; //Отмечаем ли выходные дни
                    bufDate = new Date(this.current_date.getFullYear(), this.current_date.getMonth(), i);
                    strCurDate = new Date(bufDate.getTime() - (bufDate.getTimezoneOffset() * 60000)).toISOString().split("T")[0];
                    daysIsMarked = false;
                    MarkedStyle = "";
                    MarkedTitle = "";
                    for (let md = 0; md < this.mark_days.length; md++) {//Обработка массива отмеченных дней
                        if (this.mark_days[md].date == strCurDate) {
                            daysIsMarked = true;
                            MarkedStyle = this.mark_days[md].style;
                            MarkedTitle = this.mark_days[md].title;
                            break;
                        }
                    }
                    days.push({
                        number: i,
                        isActive: false,
                        isVisible: true,
                        isWeekend: daysIsWeekend,
                        isMarked: daysIsMarked,
                        MarkedStyle: MarkedStyle,
                        MarkedTitle: MarkedTitle,
                    });
                    day_week++;
                }
                if (days.length > 0) {//Если последняя неделя не полная
                    this.weeks.push({days});
                }
                while (this.weeks.length < 6) {//Дополнить до 6 недель
                    this.weeks.push({
                        days: [{
                            number: "",
                            isActive: false,
                            isVisible: false,
                            isWeekend: false,
                            isMarked: false
                        }]
                    });
                }
                if (this.set_mouth == this.current_date.getMonth() && this.set_year == this.current_date.getFullYear()) {
                    this.chose_date(this.current_date.getDate());
                }
            },
            chose_date(number) {//Метод - выбор даты
                if (number != "") {
                    this.current_date = new Date(this.current_date.getFullYear(), this.current_date.getMonth(), number);
                    for (let w = 0; w < this.weeks.length; w++) {//Цикл перебора недель
                        for (let d = 0; d < this.weeks[w].days.length; d++) {//Цикл перебора дней недели
                            if (this.weeks[w].days[d].number === this.last_day) {
                                this.weeks[w].days[d].MarkedStyle = this.last_style;
                            }
                        }
                    }
                    for (let w = 0; w < this.weeks.length; w++) {//Цикл перебора недель
                        for (let d = 0; d < this.weeks[w].days.length; d++) {//Цикл перебора дней недели
                            if (this.weeks[w].days[d].number === number) {//Ищем выбранный день
                                this.last_day = number;
                                this.last_style = this.weeks[w].days[d].MarkedStyle;
                                this.weeks[w].days[d].MarkedStyle = "";
                                this.weeks[w].days[d].isActive = true;//Делаем активным
                            } else {//Остальные деактивируем
                                this.weeks[w].days[d].isActive = false;
                            }
                        }
                    }
                    this.set_mouth = this.current_date.getMonth();
                    this.set_year = this.current_date.getFullYear();
                    if (this.event_init) {
                        this.$emit('DateSelected', new Date(this.current_date.getTime() - (this.current_date.getTimezoneOffset() * 60000)).toISOString().split("T")[0]);
                    } else {
                        this.event_init = true;
                    }
                }
            }
        },
        mounted() {
            this.showSelected = (typeof(this.showSelected) !== "boolean") ? this.showSelected : true;
            this.set_mouth = this.current_date.getMonth();
            this.set_year = this.current_date.getFullYear();
            this.date_colspan = (this.show_buttons) ? 5 : 7;
            if (this.mark_weekend) {
                for (let i = 0; i < this.days.length; i++) {//Пустые блоки
                    if (i > 4) {
                        this.name_days.push(true);
                    } else {
                        this.name_days.push(false);
                    }
                }
            }
            this.initial_month();
        }
    }
</script>

<style scoped>
    .calendar {
        display: inline-block;
        margin: auto;
        border: solid 3px black;
        padding: 12px;
        border-radius: 18px;
        box-shadow: 0 0 20px rgba(78, 61, 61, 0.5);
    }
    table {
        display: inline;
        text-align: center;
    }
    table tr {
        height: 30px;
    }
    table td {
        width: 30px;
    }
    .Visible, .button {
        cursor: pointer;
    }
    .Visible {
        border: solid 1px black;
        border-radius: 2px;
    }
    .WeekendName {
        color: red;
    }
    .Weekend {
        border: solid 1px black;
        background: rgb(255, 190, 190);
    }
    .Marked {
        border: solid 1px black;
        background: rgb(255, 190, 190);
    }
    .Active {
        background: red;
    }
</style>
