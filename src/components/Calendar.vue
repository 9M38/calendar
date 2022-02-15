<template>
    <div class="calendar-wrap">
        <div class="calendar-header">
            <button @click="prevMonth()">prev</button>
            {{ $d(date,'calendarHeader' )}}
            <button @click="nextMonth()">next</button>
        </div>
        <div class="calendar-days">
            <div class="day-name" v-for="day,i in daysOfWeek" :key="i">
                {{ $d(day,'calendarDays')}}
            </div>
        </div>
        <div class="calendar-dates">
            <div class=" null-cell cell" v-for="nulldate,i in preNullDates" :key="'pre'+i">
                null
            </div>
            <div class="date-cell cell" v-for="date, j in validDates" :key="'date'+j" @click="emitDate(j)">
                {{date.getDate()}}
            </div>
            <div class="null-cell cell" v-for="nulldate, k in postNullDates" :key="'post'+k">
                null
            </div>
        </div>
    </div>
</template>

<script>
    export default {

        name: "Calendar",

        props: {
            initdate: {
                type: Date,
                default: function () {
                    return new Date()
                }
            }
        },

        emits: {
            datepick: {
                type: Date
            }
        },

        data() {
            return {
                date: this.initdate,
                validDates: [],
                daysOfWeek: [],
                DEBUG: true,
                preNullDates:[],
                postNullDates:[],
            }
        },


        methods: {
            GetvalidDates() {
                // Fills the list of valud dates (UTC)
                this.validDates = []
                let truthMonth = this.date.getMonth()
                let offset = this.date.getTimezoneOffset()
                for (let i = 1; i <= 31; i++) {
                    let newDate = new Date(this.date.getFullYear(), this.date.getMonth(), i, 0, 0, 0)
                    if (newDate.getMonth() != truthMonth) {
                        break
                    }
                    let offsetTime = +newDate - (offset * 60 * 1000)
                    let offsetDate = new Date(offsetTime)
                    this.validDates.push(offsetDate)
                }
                this.GetNullDates()
            },

            GetNullDates(){
                // fills NullDates with nothing desired number of times
                this.preNullDates = []
                this.postNullDates = []

                let firstDay = this.validDates[0].getDay()
                let lastDay = this.validDates.at(-1).getDay()
                if (firstDay ==0){
                    firstDay = 7
                }
                for (let i=0;i<firstDay-1;i++){
                    this.preNullDates.push('null')
                }
                if (lastDay != 0){
                for (let i=0;i<7-lastDay;i++){
                    this.postNullDates.push('null')
                }
                }
            },

            InitDaysOfWeek() {
                // initiates an array of 7 elements, 
                // each one is a Date object, starting at monday 
                // and ending on Sunday
                // for reference we'll take 8st Febuary 1970, which is Monday 
                let monday = new Date(1970, 1, 8)
                this.daysOfWeek = []
                for (let i = 0; i <= 6; i++) {
                    monday = +monday + (1000 * 3600 * 24)
                    this.daysOfWeek.push(new Date(monday))
                }
            },

            prevMonth() {
                let month = this.date.getMonth()
                let shiftedDate = this.date.setMonth(month - 1)
                let shiftedDateobject = new Date(shiftedDate)
                if (shiftedDateobject.getMonth() == month) {
                    // may happen when this date is March 31th and we are gonna 
                    // get to prev month Feb 31th => results in March 2th or 1st...
                    do {
                        shiftedDate -= (1000 * 3600 * 24)
                        shiftedDateobject = new Date(shiftedDate)
                    } while (
                        shiftedDateobject.getMonth() == month
                    )
                }

                this.date = new Date(shiftedDate)
                this.GetvalidDates()

            },
            nextMonth() {
                let month = this.date.getMonth()
                let shiftedDate = this.date.setMonth(month + 1)
                let shiftedDateobject = new Date(shiftedDate)
                if (shiftedDateobject.getMonth() == month) {
                    // may happen when this date is March 31th and we are gonna 
                    // get to next month Apr 31th => results in May 1th
                    do {
                        shiftedDate -= (1000 * 3600 * 24)
                        shiftedDateobject = new Date(shiftedDate)
                    } while (
                        shiftedDateobject.getMonth() == month
                    )
                }

                this.date = new Date(shiftedDate)
                this.GetvalidDates()
            },

            emitDate(i) {
                console.log('emit')
                this.$emit('datepick', this.validDates[i])
                if (this.DEBUG) {
                    console.log('emitted:', this.validDates[i])
                }
            }
        },

        created() {
            this.GetvalidDates()
            this.InitDaysOfWeek()
            
        }
    }
</script>

<style lang="scss" scoped>

    *{
        box-sizing: border-box;
        margin: 0;
        
    }

    $cell-width: 40px;

    .calendar-wrap{
        margin: auto;
        width: calc($cell-width * 7 + 4px);
        border: 2px rgb(230, 230, 230) solid;
    }

    .calendar-header{
        align-items: center;
        display: flex;
        justify-content: space-between;
        height: $cell-width;
    }

    .day-name{
        width: $cell-width;
        border: 3px solid transparent;
        display: inline-block;
    }


    .date-cell{
        height: $cell-width;

        &:hover{
            border: 3px solid lightblue;
            cursor: pointer;
        }
    }
    .cell{
        height: $cell-width;
        width: $cell-width;
        border: 3px solid transparent;
        display: inline-block;
        padding-top: 10px;
    }
    .null-cell{
        color: transparent;
        
    }

    .calendar-dates{
        width: calc($cell-width * 7);
    }

</style>