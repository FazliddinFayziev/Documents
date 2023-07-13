## Upload file to firebase Store with Node JS (multer library)

##### Here We will store the any image to firebase Storage and put it is URL to MongoDb Database

### 🌱 First

### install all needed libraries
```
    npm install express
    npm install cors
    npm install firebase-admin
    npm install multer
    
```

### 🌱 Second

#### In second step, I will show you how to upload Array of images to firebase Storage and put its URL to MongoDb Database

### Array of images upload

### Create and open index.js file and copy paste code that is in below. In this code, We will do 3 steps.

##### 1) connect our MongoDb
##### 2) connect our firebase Storage
##### 3) configure our multer 

```javascript
    const express = require('express');
    const app = express();
    const admin = require('firebase-admin');
    const multer = require('multer');
    const { uploadImage } = require('./image');

    // You need to download this file from your firebase (
        // Go you project setting and Service Account and click on generate new private key. Then It will create for you new file. You need to put this file instead of AccountKet.json file
    // )
    const serviceAccount = require('./AccountKey.json');

    app.use(express.json());
    app.use(cors());

    // Connect to MongoDB (STEP - 1)
    mongoose
        .connect('mongodb+srv://<username>:<password>@cluster0.  soswtmt.mongodb.net/products?retryWrites=true&w=majority', {
        useNewUrlParser: true,
        useUnifiedTopology: true,
    })
    .then(() => console.log('Database is connected well'))
    .catch((err) => console.log(There is a problem: ${err}));

    // Initialize Firebase Admin and connect our firebase storage url (STEP - 2)
    admin.initializeApp({
        credential: admin.credential.cert(serviceAccount),
        storageBucket: '<url of your firebase Storage>',
    });

    // Configure Multer (STEP - 3)
    const storage = multer.memoryStorage();
    const upload = multer({ storage: storage });

    app.get('/', (req, res) => {
        res.send('Hello World');
    });

    app.post('/upload', upload.array('images'), async (req, res) => {
    try {
    
        // upload image logic (funcition)
        const imageUrls = await uploadImage(req.files, res);

        // new Product
        const product = new Product({
            images: imageUrls
        });

        // save and send product
        const savedProduct = await product.save();
        res.status(200).send(savedProduct);

    } catch (error) {

        console.error(error);
        res.status(500).json({ error: 'Failed message' });

    }
});

app.listen(3000, function () {
    console.log('Server is running on port 3000');
});
```

### 🌱 Third

### Create and open image.js file

```javascript
const admin = require('firebase-admin');

async function uploadImageArray(images) {
    if (!images || images.length === 0) {
        return res.status(400).json({ error: 'No image file     provided' });
    }

    const bucket = admin.storage().bucket();
    const filePromises = [];

    for (const file of images) {
        const readyFile = bucket.file(file.originalname);
        const fileStream = readyFile.createWriteStream({
            metadata: {
                contentType: file.mimetype,
            },
        });

        const filePromise = new Promise((resolve, reject) => {
            fileStream.on('error', (err) => {
                console.error(err);
                reject(err);
            });

            fileStream.on('finish', () => {
                readyFile.makePublic()
                    .then(() => {
                        const imageUrl = `https://storage.googleapis.com/${bucket.name}/${readyFile.name}`;
                        resolve(imageUrl);
                    })
                    .catch((err) => {
                        console.error(err);
                        reject(err);
                    });
            });

            fileStream.end(file.buffer);
        });

        filePromises.push(filePromise);
    }

    const imageUrls = await Promise.all(filePromises);
    return imageUrls
}

exports.uploadImage = uploadImageArray;
```

### On this exapmle I did not explain about Schemas and Models and so on. We just learned upload image to firebase Storage and put it is url to MongoDb Database. Thank you!!!

