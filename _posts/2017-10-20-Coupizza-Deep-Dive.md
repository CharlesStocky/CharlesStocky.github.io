I've made this post to explain in detail how I went about coding the API for Coupizza.com. The code is in a constant state of change, but it's currently at a point where I feel somewhat comfortable with it being in production, and believe it to be somewhat sustainable in its current state. I started the process of creating Coupizza with a source of data insertion, so it seems like a natural place to start this post.  

```javascript 
let d = new Date;
let currentMonth = d.getMonth() + 1 ;
let currentDate = d.getDate();
let currentYear = d.getFullYear();
let currentDateObj = {
  month: currentMonth,
  day: currentDate,
  year: currentYear
}

let dateFormatter = (object) => { //formats date to match the formatting of facebook dates
  if(object.month < 10){ 
    object.month = '0' + object.month;
  }
  if(object.day < 10){ 
    object.day = '0' + object.day;
  }
  let formattedDate = object.year + '-' + object.month + '-' + object.day  
  return formattedDate
}

let promoArr = []
let currentDateStr = dateFormatter(currentDateObj)
```
