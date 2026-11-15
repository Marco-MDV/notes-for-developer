# Sign in with Google using Passport

Use Passport's Google OAuth 2.0 strategy to let users sign in to an Express application with their Google account.

## Configure Google

1. Create or select a project in [Google Cloud Console](https://console.cloud.google.com/).
2. Configure the consent screen and add your Google account as a test user if the app is in testing mode.
3. Create an OAuth client of type **Web application**.
4. Add `http://localhost:3000/auth/google/callback` to **Authorized redirect URIs**. The value must match the callback URL your application sends, including its scheme, host, port, and path. Register the HTTPS callback separately for production.
5. Copy the client ID and client secret to your server environment.

See [Google's OAuth 2.0 documentation](https://developers.google.com/identity/protocols/oauth2/web-server).

## Create the Express app

Start in an npm project, then install the dependencies.

```sh
npm install express express-session passport passport-google-oauth20
```

Create an `.env` file with `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET`, `GOOGLE_CALLBACK_URL`, and `SESSION_SECRET`. Set `GOOGLE_CALLBACK_URL` to the redirect URI you registered. Generate a random session secret with the command below and keep the file out of version control.

```sh
node -e "console.log(require('node:crypto').randomBytes(32).toString('hex'))"
```

Save the example as `google-login.cjs`.

```js
const express = require('express');
const session = require('express-session');
const passport = require('passport');
const { Strategy: GoogleStrategy } = require('passport-google-oauth20');

const {
    GOOGLE_CLIENT_ID,
    GOOGLE_CLIENT_SECRET,
    GOOGLE_CALLBACK_URL,
    SESSION_SECRET,
} = process.env;

if (!GOOGLE_CLIENT_ID || !GOOGLE_CLIENT_SECRET || !GOOGLE_CALLBACK_URL || !SESSION_SECRET) {
    throw new Error('Set the Google OAuth credentials, callback URL, and session secret.');
}

const app = express();

app.use(session({
    secret: SESSION_SECRET,
    resave: false,
    saveUninitialized: false,
    cookie: { httpOnly: true, sameSite: 'lax' },
}));
app.use(passport.initialize());
app.use(passport.session());

passport.use(new GoogleStrategy({
    clientID: GOOGLE_CLIENT_ID,
    clientSecret: GOOGLE_CLIENT_SECRET,
    callbackURL: GOOGLE_CALLBACK_URL,
    state: true,
}, (_accessToken, _refreshToken, profile, done) => {
    done(null, { googleId: profile.id, displayName: profile.displayName });
}));

passport.serializeUser((user, done) => done(null, user));
passport.deserializeUser((user, done) => done(null, user));

app.get('/', (_req, res) => {
    res.send('<a href="/auth/google">Sign in with Google</a>');
});

app.get('/auth/google', passport.authenticate('google', { scope: ['profile'] }));

app.get('/auth/google/callback',
    passport.authenticate('google', { failureRedirect: '/' }),
    (_req, res) => res.redirect('/me')
);

app.get('/me', (req, res) => {
    if (!req.isAuthenticated()) return res.sendStatus(401);

    res.json(req.user);
});

app.listen(3000, () => console.log('Open http://localhost:3000'));
```

Run the app with a supported Node.js LTS release and open `http://localhost:3000`.

```sh
node --env-file=.env google-login.cjs
```

The first route starts the sign-in flow. Google returns the user to the callback route, where Passport checks the OAuth state and establishes the session. The example requests the profile scope and uses Google's profile ID to identify the user. Request the email scope only if your application actually needs it.

For a deployed application, use a persistent session store and HTTPS with secure cookies. If you keep a local user database, find or create the account using the Google profile ID and serialize your local user ID. The example uses the default in-memory store for local development.

See [Passport's Google strategy documentation](https://www.passportjs.org/packages/passport-google-oauth20/) and the [Express session documentation](https://expressjs.com/en/resources/middleware/session.html).
