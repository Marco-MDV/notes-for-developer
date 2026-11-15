# Back-end development

A client sends requests and receives responses. A server handles those requests, applies application rules, and reads or writes data when it needs to. The database stores that data and can run separately from the server.

## Node.js and Express

Node.js runs JavaScript outside the browser. It provides APIs such as `node:fs` for working with files. Browser globals such as `window` are specific to the browser.

Express is a Node.js framework for HTTP servers. A route handles an HTTP method such as GET, POST, PUT, PATCH, or DELETE at a given path. Middleware can log requests, parse request bodies, check authentication and authorization, serve static files, or handle errors.

### Middleware

Use `app.use()` for middleware that applies across routes or to a mounted router. Pass middleware to a route when it applies only to that route. A middleware function either sends a response or calls `next()` to continue. Error-handling middleware takes four arguments and is registered after the routes.

```sh
mkdir express-notes
cd express-notes
npm init -y
npm install express
```

Save this example as `app.cjs` and run `node app.cjs`.

```js
const express = require('express');

const app = express();

function logRequest(req, res, next) {
    console.log(req.method, req.path);
    next();
}

function requireName(req, res, next) {
    if (typeof req.body?.name !== 'string' || !req.body.name.trim()) {
        return res.status(400).json({ message: 'A name is required.' });
    }

    next();
}

app.use(logRequest);
app.use(express.json());

app.post('/greetings', requireName, (req, res) => {
    res.status(201).json({ message: 'Hello, ' + req.body.name.trim() + '!' });
});

app.use((error, req, res, next) => {
    if (res.headersSent) return next(error);

    if (error.code === 'LIMIT_FILE_SIZE') {
        return res.status(413).json({ message: 'The upload is too large.' });
    }
    if (error.name === 'MulterError' || error.status === 400) {
        return res.status(400).json({ message: 'Invalid request body.' });
    }

    console.error(error);
    res.status(500).json({ message: 'Internal server error.' });
});

app.listen(3000, () => console.log('Listening on http://localhost:3000'));
```

Multiple route middleware functions can be passed in order, such as `app.post('/greetings', [first, second], handler)`. In Express 5, a route or middleware that returns a rejected promise forwards the error to the error handler.

See [Express middleware](https://expressjs.com/en/guide/using-middleware.html) and [error handling](https://expressjs.com/en/guide/error-handling.html).

### File uploads with Multer

Multer processes requests sent as `multipart/form-data`. The form field name must match the name passed to the upload middleware.

```sh
npm install multer
```

```html
<form action="/profile" method="post" enctype="multipart/form-data">
    <label for="avatar">Avatar</label>
    <input id="avatar" name="avatar" type="file" required>
    <button type="submit">Upload</button>
</form>
```

Add the route before the error handler in the Express example.

```js
const multer = require('multer');
const upload = multer({
    dest: 'uploads/',
    limits: { fileSize: 5 * 1024 * 1024 },
});

app.post('/profile', upload.single('avatar'), (req, res) => {
    if (!req.file) return res.status(400).json({ message: 'An avatar is required.' });

    res.status(201).json({ id: req.file.filename });
});
```

Use `upload.array('photos', 12)` for multiple files in the same field, or `upload.fields([{ name: 'avatar', maxCount: 1 }, { name: 'gallery', maxCount: 8 }])` for separate fields. See the [Multer documentation](https://expressjs.com/en/resources/middleware/multer.html).

### Upload to Cloudinary

Use Multer to read the file and the Cloudinary SDK to upload it. This example keeps a small upload in memory and sets a size limit. Set `CLOUDINARY_URL` in the server environment using credentials from your Cloudinary dashboard. Add this route before the Express error handler.

```sh
npm install multer cloudinary
```

```js
const multer = require('multer');
const cloudinary = require('cloudinary').v2;

const imageUpload = multer({
    storage: multer.memoryStorage(),
    limits: { fileSize: 5 * 1024 * 1024 },
});

app.post('/upload', imageUpload.single('image'), async (req, res) => {
    if (!req.file) return res.status(400).json({ message: 'An image is required.' });

    const result = await new Promise((resolve, reject) => {
        const stream = cloudinary.uploader.upload_stream(
            { resource_type: 'image', folder: 'example-images' },
            (error, uploadedImage) => {
                if (error) reject(error);
                else resolve(uploadedImage);
            }
        );

        stream.once('error', reject);
        stream.end(req.file.buffer);
    });

    res.status(201).json({ id: result.public_id, url: result.secure_url });
});
```

If both examples are in the same file, reuse the existing `multer` import. See the [Cloudinary Node.js upload guide](https://cloudinary.com/documentation/node_image_and_video_upload).

### Password hashing with bcrypt

Hash a password before storing it. During sign-in, compare the submitted password with the stored hash. The second argument to `bcrypt.hash()` is a cost factor. bcrypt uses at most 72 bytes of password input, so validate the byte length consistently at registration and sign-in.

```sh
npm install bcrypt
```

```js
const bcrypt = require('bcrypt');

async function hashPassword(password) {
    if (Buffer.byteLength(password, 'utf8') > 72) {
        throw new RangeError('Password must be at most 72 bytes.');
    }

    return bcrypt.hash(password, 12);
}

async function verifyPassword(password, storedHash) {
    if (Buffer.byteLength(password, 'utf8') > 72) return false;
    return bcrypt.compare(password, storedHash);
}
```

See the [bcrypt package documentation](https://www.npmjs.com/package/bcrypt).

## MongoDB with Mongoose

MongoDB stores documents in collections. You can work with it through MongoDB Compass, the Atlas interface, or an application. In Node.js, a Mongoose schema defines fields and validation for documents handled by a model.

```sh
npm install express mongoose
```

Create an `.env` file containing `MONGODB_URI` and, optionally, `PORT`. Keep credentials out of version control. Save the example as `server.cjs`, then run `node --env-file=.env server.cjs` on a supported Node.js LTS release.

```js
const express = require('express');
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    name: { type: String, required: true, trim: true },
    surname: { type: String, required: true, trim: true },
    age: { type: Number, required: true, min: 0 },
    email: { type: String, required: true, unique: true, trim: true, lowercase: true },
});

const User = mongoose.model('User', userSchema);
const users = express.Router();
const app = express();

app.use(express.json());
app.use('/users', users);

users.post('/', async (req, res) => {
    const { name, surname, age, email } = req.body ?? {};
    const user = await User.create({ name, surname, age, email });
    res.status(201).json(user);
});

app.use((error, req, res, next) => {
    if (res.headersSent) return next(error);
    if (error.code === 11000) {
        return res.status(409).json({ message: 'Email already registered.' });
    }
    if (error instanceof mongoose.Error.ValidationError || error.status === 400) {
        return res.status(400).json({ message: 'Invalid user data.' });
    }

    console.error(error);
    res.status(500).json({ message: 'Internal server error.' });
});

async function start() {
    await mongoose.connect(process.env.MONGODB_URI);
    await User.init();

    const port = Number(process.env.PORT ?? 3000);
    app.listen(port, () => console.log('Listening on port ' + port));
}

start().catch(error => {
    console.error(error.message);
    process.exitCode = 1;
});
```

The server starts after connecting to MongoDB and initializing the model. The schema's `unique` option declares a database index, so duplicate values produce a MongoDB duplicate-key error. As the project grows, you can move the model and router into separate files and mount the exported router with `app.use()`.

See the Mongoose guides to [connections](https://mongoosejs.com/docs/connections.html) and [validation](https://mongoosejs.com/docs/validation.html).
