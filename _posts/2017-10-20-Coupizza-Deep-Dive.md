I've made this post to explain in detail how I went about coding the API for Coupizza.com. The code is in a constant state of change, but it's currently at a point where I feel somewhat comfortable with it being in production, and believe it to be somewhat sustainable in its current state. I started the process of creating Coupizza with a source of data insertion, so it seems like a natural place to start this post.  

```javascript 
let getCodes = (posts, locale) =>{
  if(posts.created_time.includes(currentDateStr)){
    if(posts.message){
      console.log(posts.message)
      if(posts.message.toLowerCase().match(/code/)){
//Customer appreciation day 25% off regular menu prices. Use promo code Wayne25
        let indxOfCodeStart = posts.message.toLowerCase().indexOf('code')
        let promoSlice = posts.message.slice(indxOfCodeStart + 4)
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
        }else{
          let promoCode = posts.message.slice(indxOfCodeStart + 5) //if the code isn't capitalized, match whatever comes after the string "code"
          console.log(promoCode)
          let promoObj= {
            promoCode: promoCode,
            promoLocation: locale
          }
          return promoObj
        }
      }else if(posts.message.toLowerCase().match(/promo/) && !posts.message.toLowerCase().match(/code/)){ //for matches that only use 'promo'
        let indxOfPromoStart = posts.message.toLowerCase().indexOf('promo')
        let promoSlice = posts.message.slice(indxOfPromoStart + 5) //+5 to exclude 'promo' from the slice
        if(promoSlice.match(/[A-Z\d]{3,}/)[0]){
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
        }else{
          let promoCode = promoSlice.slice(indxOfPromoStart)
        }
      }
    }else{
      console.log(posts)
      return {'error': "id likely doesn't exist"}
    }
  }
}
```
