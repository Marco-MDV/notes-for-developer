# Google tools for web app

When you want create a good web app maybe you need to use different tools for create a good UX / UI and create a safe experience for user, maybe you using struments "offered" by google.

## Passport Google

Maybe when a user click a button "Google" for registration or login the developing as creator the services with the "<a href='https://www.passportjs.org/'>Passport</a>" service. </br> (in detail chose: <a href='https://www.passportjs.org/packages/passport-google-oauth20/'>passport-google-oauth20</a>)
frist to write code you set the <a href='https://console.cloud.google.com/'>API google</a>  :
1. click on "<a href='https://console.cloud.google.com/apis/library'>Library</a>"
2. search "People API"
3.  set the API:
    - In "Name" set name of your application
    - In "Origini JavaScript autorizzate" set the backend address
    - In "URI di reindirizzamento autorizzati" set the backend address plus path "/auth/google/callback"
4. take "ID client" and "Client secret" 

What to do for using this? it's realy seample:
1. Middleware
2. Write this code:
```
"const GoogleStrategy = require('passport-google-oauth20').Strategy;"

const googleStrategy = (new GoogleStrategy({
    clientID: GOOGLE_CLIENT_ID,
    clientSecret: GOOGLE_CLIENT_SECRET,
    callbackURL: "http://www.example.com/auth/google/callback"
  },
  function(accessToken, refreshToken, profile, cb) {
    User.findOrCreate({ googleId: profile.id }, function (err, user) {
      return cb(err, user);
    });
  }
));

module.exports = googleStrategy
```
3. Creat file for google login, regustration or both
4. Write this code:
```
const passport = require('passport');
const googleStrategy = require('../examplePath/oauthGoogle')
passport.use('google', googleStrategy)

google.get('/auth/google', passport.authenticate('google', { scope: ['profile', 'email'] })); 


google.get('/auth/google', passport.authenticate('google', { session: false, failureRedirect: `example/path/login` }), async function (req, res, next){
    try{
        const googleUser = await yourUserSchema.findOne({ Email: req.user._json.email }) //using req.user for get only essential info 
        if(!googleUser){
            ...logic for creat a user and update in db
        }else{
            ...logic take data and to do something
        }
    }catch(error){
        next({error: error.message, status: 500});
    }
})
```

Now you can write the good passport , if you want request me something write me on github or linkeding

p.s.
Never is free from Google if you're not pay in cash, you will pay in information.