I've made this post to explain in detail how I went about coding the API for Coupizza.com. The code is in a constant state of change, but it's currently at a point where I feel somewhat comfortable with it being in production, and believe it to be somewhat sustainable in its current state. I started the process of creating Coupizza with a source of data insertion, so it seems like a natural place to start this post.  

```javascript 
let getCodes = (posts, locale) =>{
  if(posts.created_time.includes(currentDateStr)){
```
The getCodes function accepts objects from the FB Graph 'feed' API. There seems to be no rhyme or reason to the date associated with post objects, so my first objective was to create a way of formatting/producing a date that matched with the formatting of the FB api to get posts, and thus coupon codes, posted within a given time period.       

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
```
This code is the date formatter/getter for the coupon parsing. Nothing too eventful, the FB API places zeroes in front of the number associated with a month/date that falls below 10 , so it adds a zero if necesary and places a dash between the the pieces of the date string. From here, the meat of the API follows.     

```javascript
   console.log(posts.message)
      if(posts.message.toLowerCase().match(/code/)){
        let indxOfCodeStart = posts.message.toLowerCase().indexOf('code')
        let promoSlice = posts.message.slice(indxOfCodeStart + 4)
```
First, it logs the posts' 'message' object property to monitor how well the code is performing its string parsing duties. It checks the posts' message for the string: 'code'. It uses ```str.match()``` instead of ```str.includes()``` to avoid 'matching' to any instance of code, IE 'encoded','decoded', etc. It then places the index of the string: 'code' inside of the ```indxOfCodeStart``` variable. Everything before that isn't needed, so it slices from ```indxOfCodeStart``` (+ 4 to avoid including 'code' in the slice) to the end of message string.           

```javascript
        if(promoSlice.match(/[A-Z\d]{3,}/)){
          let promoCode = promoSlice.match(/[A-Z\d]{3,}/)[0]
          if(promoCode === ''){
            return false;
          }
          let promoObj = {
            promoCode: promoCode,
            promoLocation: locale
          }
          console.log(promoObj)
          return promoObj
```
The sliced message string is then checked for a substring that contains numbers and/or capitalized letters that is at least 3 characters in length. The ```match()``` method returns an array, the 0th index being the part we're interested in, so it's placed inside of the ```promoCode``` variable. It returns false if the slice is non existent, as sometimes message bodies will include 'code' as the last word in the message. It places the promo code inside of an object, as well as the locale which is passed directly from the FB API into this code. It logs the object, then returns it.          

