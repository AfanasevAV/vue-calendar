<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-3 text-center mt-5">
                <calendar :data="data" :showSelected="true" @DateSelected="setDate"/>
            </div>
        </div>
        <div class="row justify-content-center">
            <input class="date" type="text" v-model="inp_date">
        </div>
    </div>
</template>

<script>
    import calendar from './components/calendar'

    export default {
        name: 'App',
        components: {
            calendar
        },
        data() {
            return {
                inp_date: "",
                data: {
                    date: "2020-07-01",
                    mark_days: [
                        {date: "2020-07-13", style: "background: green;", title: "Эта дата отмечена зеленым цветом."},
                        {date: "2020-07-14", style: "background: yellow; border: solid 1px blue;"},
                        {date: "2020-07-15", style: "font-weight: 800; font-style: italic;", title: "У этой даты жирный, наклонный шрифт."},
                    ],
                    // days: ["Пн", "Вт", "Ср", "Чт", "Пт", "Сб", "Вс"],
                    // months: ["Январь", "Февраль", "Март", "Апрель", "Май", "Июнь", "Июль", "Август", "Сентябрь", "Октябрь", "Ноябрь", "Декабрь"],
                    // mark_weekend: false,
                    // show_years: false,
                    // show_buttons: false,
                    // event_init: false,
                },
            };
        },
        methods: {
            setDate: function (new_date) {
                this.inp_date = new_date;
            }
        }
    }
</script>

<style>
    input {
        text-align: center;
        display: block;
        margin: 22px;
    }
</style>
