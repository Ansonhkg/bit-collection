## Expected Usage

```js
import DateRangePicker from '@bit/ansonhkg.collection.faros-date-range-picker';

// vue.js
export default{
    components:{
        DateRangePicker
    }
}

// nuxt.js
export default{
    DateRangePicker: () => import('@bit/ansonhkg.collection.faros-date-range-picker')
}
```

```html
<template>
    <DateRagenPicker />
</template>
```
## Events

| Event                 | Returns |
| ---                   | ---     |    
| onProduceChart        | Object  |

> ***onProduceChart properties***
> | Property | Type | Example |
> | ---      | ---  | ---     | 
> | dayMode | Boolean | true/false |
> | selectedStartDate | String | '2009-01-01'
> | selectedEndDate | String | '2020-02-02'
> | raw | Object | calendarObject

## Properties

| Property    | Type    | Example |
| ---         | ---     | ---     |
| demo        | Boolean | true or false
| scopeStart  | String  | '2019-01-01'
| scopeEnd    | String  | '2020-01-01'
| shortcuts   | Object  | ```[{title: 'One Day By Half Hour', number: null, unit: null}, {title: 'One Week by Half Hour', number: 1, unit: 'weeks'}]```

### Shortcut Object Properties
| Property    | Type    | Example | Options |
| ---         | ---     | ---     | --- | 
| title        | String | One Day By Half Hour | any |
| number | Number | 1 | null |
| unit | String | weeks | days, weeks, months, years |


## Test Cases
- Shortcut should reset after calendar dates is clicked
- Day dates should reset after a shortcut is clicked