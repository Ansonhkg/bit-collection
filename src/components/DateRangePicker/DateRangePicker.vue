<template>
    <div class="drp">

        <!-- shortcuts wrapper -->
        <div class="drp-shortcuts">
            <!-- title -->
            <div class="drp-shortcuts__title">
                <span>Shortcuts</span>
            </div>

            <!-- shortcuts -->
            <ul class="drp-shortcuts__list">
                
                <!-- shortcut -->
                <li class="drp-shortcuts__list__item drp-shortcuts__list__item--active">
                    <div class="drp-shortcuts__list__item__title">One Day By Half Hour</div>
                    <div class="drp-shortcuts__list__item__date">26 Feb 2020</div>
                </li>

                <!-- shortcut -->
                <li class="drp-shortcuts__list__item">
                    <div class="drp-shortcuts__list__item__title">One Week By Half Hour</div>
                    <div class="drp-shortcuts__list__item__date">26 Feb 2020</div>
                </li>
            </ul>

        </div>

        <!-- calendar -->
        <div class="drp-calendar">
        
            <!-- header -->
            <div class="drp-calendar__header">
                <i @click="onShiftCalendar('last_year')" class="material-icons">first_page</i>
                <i @click="onShiftCalendar('last_month')" class="material-icons">chevron_left</i>
                
                <div class="drp-calendar__header__results">
                    <div class="drp-calendar__header__results--month">
                        <span>{{ calendarMonth }}</span>
                    </div>
                </div>
                <i @click="onShiftCalendar('next_month')" class="material-icons">chevron_right</i>
                <i @click="onShiftCalendar('next_year')" class="material-icons">last_page</i>
            </div>

            <!-- dates -->
            <div class="drp-calendar__dates">
                <ul>
                    <!-- weekdays -->
                    <li v-for="day in getCalendarWeekDays('dd')" :key="day" class="drp-calendar__dates--weekday"><span>{{ day }}</span></li>

                    <!-- days -->
                    <ul v-for="(dayObj, idx) in calendarDates" :key="idx" >
                        <!-- if not enabled -->
                        <li v-if="!dayObj.enabled" class="drp-calendar__dates--disabled"><span>-</span></li>

                        <!-- enabled -->
                        <li v-else @click="onSelectDayClass(dayObj)" :class="getSelectedDayClass(dayObj)"><span>{{ dayObj.date | day }}</span></li>
                    </ul>
                </ul>
            </div>

        </div>

        <!-- produce chart -->
        <div v-if="selectedStartDate !== ''" class="drp-produce-chart">
            <div class="drp-produce-chart__selected-dates">{{ selectedStartDate | uk_format}}{{ (selectedEndDate) != '' ? ' ~ ' : '' }}{{ selectedEndDate | uk_format}}</div>
            <div @click="onSelectProduceChart">Produce Chart</div>
        </div>

        <!-- demo results -->
        <div v-if="demo">
            scopeStart: {{ scopeStart }} <br>
            scopeEnd: {{ scopeEnd }} <br>
            calendarDates: {{ calendarDates }} <br>
        </div>

    </div>
</template>

<script>
import moment from 'moment';

export default {

    props:{
        scopeStart:{
            default: moment().subtract(4, 'years').format('YYYY-MM-DD')
        },
        scopeEnd:{
            default: moment().format('YYYY-MM-DD')
        },
        demo:{
            default: true
        }
    },
    data(){
        return {
            shortcuts:{
                
            },
            // calendar dates
            calendarDates: [],
            calendarMonth: '', // Feb 2020
            selectedStartDate: '',
            selectedEndDate: '',
            selectedCounter: 0,
        }
    },
    created(){
        // --- set default calendar dates
        this.calendarDates = this.getCalendarDates(this.scopeEnd);
        this.calendarMonth = this.getCalendarMonth(this.scopeEnd);
    },
    methods:{

        /**
         * Get Calendar Weekdays **
         * @param { String } format default dd Mo, Tu, etc.
         * @returns { Array } of calendar weekdays 
         */
        getCalendarWeekDays(format = 'dd'){
            return [...Array(7)].map((_, i) => {
                return moment(i, 'e').startOf('week').isoWeekday(i + 1).format(format);
            })
        },

        /**
         * Get display dates of year month
         * @param { String } endDate 'YYYY-MM-DD'
         * @returns { Array } the calendar month date
         */
        getCalendarDates(endDate){
            
            // get this month number of days eg. April has 30 days
            var days = moment(endDate, 'YYYY-MM').daysInMonth()
            
            // get current year eg. 2020
            var year = moment(endDate).format('YYYY');

            // get current month eg. 04
            var month = moment(endDate).format('MM');

            // get day of the week eg. 03 = wednesday
            var dayOfWeek = moment(year + '-' + month + '-01').day();

            // prepend empty data on eg. wednesday has 3 - 1 empty fillers ['', '']
            var prependFillers = dayOfWeek <= 0 ? [] : [...Array(dayOfWeek - 1)].fill({
                enabled: false
            });

            // form array
            var dates = [];
            for(var i = 1; i <= days; i++){

                // prepend fillers on first index
                if(i <= 1){
                    // merge arrays
                    dates = prependFillers.concat(dates)
                }

                var date = moment(year + '-' + month + '-' + i, 'YYYY-MM-D').format('YYYY-MM-DD');
                var enabled = moment(date).isSameOrBefore(this.scopeEnd);

                dates.push({
                    enabled: enabled,
                    date: date
                })
            }

            return dates;
        },

        /**
         * Display on the calendar header
         * @param { String } endDate YYYY-MM-DD
         * @return { String } calendar month eg. Feb 2020
         */
        getCalendarMonth(endDate){
            return moment(endDate).format('MMM YYYY');
        },

        /**
         * Event:: when user click on a date, sets selected day class
         * @param { Object } dayObj
         * @param { Boolean } dayObj.enabled
         * @param { String } dayObj.date
         * @return { Void } inject approriate class based on user actions
         */
        onSelectDayClass(dayObj){
            var date = dayObj.date
            
            // === first click
            if(this.selectedCounter == 0){
                this.selectedCounter += 1;
                this.selectedStartDate = date
                return
            }

            // === second click
            // requirement: must be greater than start date
            if(this.selectedCounter == 1){

                // validation
                if(moment(date).isSameOrBefore(this.selectedStartDate)){
                    alert('End date must be greater than start date.');
                    return;
                }
                this.selectedCounter += 1;
                this.selectedEndDate = date;
                return
            }

            // === reset and callback
            if(this.selectedCounter >= 2){
                this.selectedCounter = 0;
                this.selectedStartDate = '';
                this.selectedEndDate = '';
                this.onSelectDayClass(dayObj);
            }
        },

        /**
         * Event:: when produce chart button is selected
         */
        onSelectProduceChart(){
            var composed_object = {
                dayMode: this.selectedEndDate == '',
                selectedStartDate: this.selectedStartDate,
                selectedEndDate: this.selectedEndDate
            }
            console.log(composed_object);

            this.$emit('onSelectProduceChart', composed_object);
        },

        /**
         * Event:: when last year button is clicked
         * @param { String } shift eg. last_year, last_month, next_year, next_month
         * @void set new calendar dates
         */
        onShiftCalendar(shift){

            var lastDay = this.calendarDates[this.calendarDates.length - 1].date;
            var date, newDate;

            switch(shift){
                case 'last_year':
                    date = moment(lastDay).subtract(1, 'years').format('YYYY-MM-DD');
                    newDate = moment(date, 'YYYY-MM-DD').format('YYYY-MM-') + moment(this.scopeStart).format('DD');
                    break;

                case 'last_month':
                    date = moment(lastDay).subtract(1, 'months').format('YYYY-MM-DD');
                    newDate = moment(date, 'YYYY-MM-DD').format('YYYY-MM-') + moment(this.scopeStart).format('DD');
                    break;

                case 'next_month':
                    date = moment(lastDay).add(1, 'months').format('YYYY-MM-DD');
                    newDate = moment(date, 'YYYY-MM-DD').format('YYYY-MM-') + moment(this.scopeEnd).format('DD');
                    break;
                    
                case 'next_year':
                    date = moment(lastDay).add(1, 'years').format('YYYY-MM-DD');
                    newDate = moment(date, 'YYYY-MM-DD').format('YYYY-MM-') + moment(this.scopeEnd).format('DD');
                    break;
            }

            // validation
            if(moment(newDate).isBefore(this.scopeStart) || moment(newDate).isAfter(this.scopeEnd)){
                alert("Cannot find data beyound this date");
                return;
            }

            this.calendarDates = this.getCalendarDates(date);
            this.calendarMonth = this.getCalendarMonth(date);

        },

        /**
         * Get the selected class (selected or in-range)
         * @param { Object } dayObj
         * @param { Boolean } dayObj.enabled
         * @param { String } dayObj.date
         * @returns { String } classes to be injected to the selected element
         */
        getSelectedDayClass(dayObj){
            var date = dayObj.date

            // selected dates
            if(date == this.selectedStartDate || date == this.selectedEndDate){
                return {'drp-calendar__dates--selected' : true}
            }

            // dates in range
            if(moment(date).isAfter(this.selectedStartDate) && moment(date).isBefore(this.selectedEndDate)){
                return {'drp-calendar__dates--in-range' : true}
            }
        },


    },

    filters:{
        day(date){
            return moment(date).format('DD')
        },
        uk_format(date){
            return date == '' ? '' : moment(date).format('DD/MM/YYYY')
        }
    }
}
</script>

<style lang="scss">
@import '~material-design-icons-iconfont/dist/material-design-icons.css';
@import './style.scss';

</style>