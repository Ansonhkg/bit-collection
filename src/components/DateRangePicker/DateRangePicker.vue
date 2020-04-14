<template>
    <div class="drp">

        <!-- ========== shortcuts wrapper ========== -->
        <div class="drp-shortcuts">
            <!-- title -->
            <div class="drp-shortcuts__title">
                <span>Shortcuts</span>
            </div>

            <!-- shortcuts -->
            <ul class="drp-shortcuts__list">
                
                <li v-for="(item, idx) in local_shortcuts" :key="idx" class="drp-shortcuts__list__item" @click="onShortcut(item)" :class="{'drp-shortcuts__list__item--active': item.title == selectedShortcut.title }">
                    <div class="drp-shortcuts__list__item__title">{{ item.title }}</div>
                    <div class="drp-shortcuts__list__item__date">{{ scopeEnd | uk_format }}</div>
                </li>
            </ul>

        </div>

        <!-- ========== calendar ========== -->
        <div class="drp-calendar">
        
            <!-- header -->
            <div class="drp-calendar__header">
                <i @click="onShiftCalendar('last_year')" class="material-icons">first_page</i>
                <i @click="onShiftCalendar('last_month')" class="material-icons">chevron_left</i>
                
                <div class="drp-calendar__header__results">
                    <div class="drp-calendar__header__results--month">
                        <span>{{ calendar.month }}</span>
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
                    <ul v-for="(dayObj, idx) in calendar.dates" :key="idx" >
                        <!-- if not enabled -->
                        <li v-if="!dayObj.enabled" class="drp-calendar__dates--disabled"><span>-</span></li>

                        <!-- enabled -->
                        <li v-else @click="onSelectDayClass(dayObj)" :class="getSelectedDayClass(dayObj)"><span>{{ dayObj.date | day }}</span></li>
                    </ul>
                </ul>
            </div>

        </div>

        <!-- ========== produce chart ========== -->
        <div v-if="calendar.selection.startDate !== ''" class="drp-produce-chart">
            <div class="drp-produce-chart__selected-dates">{{ calendar.selection.startDate | uk_format}}{{ (calendar.selection.endDate) != '' ? ' ~ ' : '' }}{{ calendar.selection.endDate | uk_format}}</div>
            <div @click="onProduceChart">Produce Chart</div>
        </div>

        <!-- demo results -->
        <div class="demo" v-if="demo">
            <h1>Details</h1>
            <table>
                <tr>
                    <th colspan="3">v.1.0.0</th>
                </tr>
                <tr>
                    <th>Property</th>
                    <th>Type</th>
                    <th>Data</th>
                </tr>
                <tr>
                    <td>scopeStart</td>
                    <td>String</td>
                    <td>{{ scopeStart }}</td>
                </tr>
                <tr>
                    <td>scopeEnd</td>
                    <td>String</td>
                    <td>{{ scopeEnd }}</td>
                </tr>
                <tr>
                    <td>validations</td>
                    <td>Array</td>
                    <td>{{ validations }}</td>
                </tr>
                <tr>
                    <td>calendar</td>
                    <td>Object</td>
                    <td class="demo-code">{{ calendar }}</td>
                </tr>
            </table>
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
        },
        shortcuts:{
            default: null
        }
    },
    data(){
        return {

            // ---- export variables upon emittion
            // calendar dates
            calendar:{
                dates: [],
                month: '', // Feb 2020
                selection:{
                    startDate: '',
                    endDate: '',
                    counter: 0,
                }
            },

            // ---- stays local
            local_shortcuts: this.shortcuts != null ? this.shortcuts : [
                {
                    title: 'One Day By Half Hour',
                    number: null,
                    unit: null,
                },
                {
                    title: 'One Week by Half Hour',
                    number: 1,
                    unit: 'weeks'
                }
            ],

            // shortcuts
            selectedShortcut: '',

            // validations
            validations:[]
        }
    },
    created(){
        // --- set default calendar dates
        this.calendar.dates = this.getCalendarDates(this.scopeEnd);
        this.calendar.month = this.getCalendarMonth(this.scopeEnd);
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
         * Get the selected class (selected or in-range)
         * @param { Object } dayObj
         * @param { Boolean } dayObj.enabled
         * @param { String } dayObj.date
         * @returns { String } classes to be injected to the selected element
         */
        getSelectedDayClass(dayObj){
            var date = dayObj.date

            // selected dates
            if(date == this.calendar.selection.startDate || date == this.calendar.selection.endDate){
                return {'drp-calendar__dates--selected' : true}
            }

            // dates in range
            if(moment(date).isAfter(this.calendar.selection.startDate) && moment(date).isBefore(this.calendar.selection.endDate)){
                return {'drp-calendar__dates--in-range' : true}
            }
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

            // === reset shortcuts
            this.selectedShortcut = '';

            // ========== operations ==========
            // === first click
            if(this.calendar.selection.counter == 0){

                // == validation
                // -- if clicked date is the same as scopeEnd date
                if(moment(date).isSame(this.scopeEnd)){
                    this.validations.push('secondClickPass');
                }

                this.calendar.selection.counter += 1;
                this.calendar.selection.startDate = date
                return
            }

            // === second click
            // requirement: must be greater than start date
            if(this.calendar.selection.counter == 1){
                
                // == validation
                // -- if validations has second click pass
                if(this.validations.includes('secondClickPass')){
                    this.validations = this.validations.filter(x => x !== 'secondClickPass');
                    reset(this);
                    return;
                }
                
                // -- if clicked date is less than start date
                if(moment(date).isSameOrBefore(this.calendar.selection.startDate)){
                    alert('End date must be greater than start date.');
                    return;
                }
                this.calendar.selection.counter += 1;
                this.calendar.selection.endDate = date;
                return
            }

            // === reset and callback
            if(this.calendar.selection.counter >= 2){
                reset(this)
            }

            function reset(_){
                _.calendar.selection.counter = 0;
                _.calendar.selection.startDate = '';
                _.calendar.selection.endDate = '';
                _.onSelectDayClass(dayObj);
            }
        },

        /**
         * Event:: when produce chart button is selected
         */
        onProduceChart(){
            var composed_object = {
                dayMode: this.calendar.selection.endDate == '',
                selectedStartDate: this.calendar.selection.startDate,
                selectedEndDate: this.calendar.selection.endDate,
                raw: this.calendar
            }
            console.log(composed_object);

            this.$emit('onProduceChart', composed_object);
        },

        /**
         * Event:: when last year button is clicked
         * @param { String } shift eg. last_year, last_month, next_year, next_month
         * @void set new calendar dates
         */
        onShiftCalendar(shift){

            var lastDay = this.calendar.dates[this.calendar.dates.length - 1].date;
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

            this.calendar.dates = this.getCalendarDates(date);
            this.calendar.month = this.getCalendarMonth(date);

        },

        /**
         * Event:: when shortcut is selected
         * @param { Object } shortcut
         * @void set selectedShortcut as item object
         */
        onShortcut(item){
            this.selectedShortcut = item;
            this.calendar.counter = 2;
            this.calendar.selection.startDate = this.getShortcutStartDate(item.number, item.unit);
            this.calendar.selection.endDate = this.scopeEnd;
        },

        /**
         * Getter:: get shortcuts start date
         * @param { Number } number number of unit
         * @param { String } unit days, weeks, months
         * @return { String } of desired date
         */
        getShortcutStartDate(number = null, unit = null){
            var inputFormat = 'YYYY-MM-DD';

            return number == null || unit == null
                   ? moment(this.scopeEnd, inputFormat).format(inputFormat)
                   : moment(this.scopeEnd, inputFormat).subtract(number, unit).add(1, 'days').format(inputFormat) 
        }


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
.demo{
    font-family: 'Consolas';
    font-size: 13px;
    th{
        text-decoration: underline;
    }
    th, td{
        padding: 10px;
        border: 0px whitesmoke solid;
        background: #1e1e1e;
        color: white;
    }
    .demo-code{
        font-size: 12px;
        background: #1e1e1e;
        color: #9cdcfe;
        text-shadow: 0px 1px 0px #1e1e83;
    }
}
</style>