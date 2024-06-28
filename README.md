mkdir tendzzy-store
cd tendzzy-store
mkdir client server
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tendzzy Store</title>
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body>
    <header class="bg-blue-500 text-white p-4">
        <h1 class="text-2xl">Tendzzy Store</h1>
    </header>
    <main class="p-4">
        <section id="products" class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4"></section>
    </main>
    <script src="app.js"></script>
</body>
</html>
const productsSection = document.getElementById('products');

fetch('http://localhost:5000/api/products')
    .then(response => response.json())
    .then(products => {
        products.forEach(product => {
            const productCard = document.createElement('div');
            productCard.className = 'border p-4';
            productCard.innerHTML = `
                <h2 class="text-xl">${product.name}</h2>
                <p>${product.description}</p>
                <p class="text-lg font-bold">${product.price} USD</p>
            `;
            productsSection.appendChild(productCard);
        });
    });
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');

const app = express();
const PORT = process.env.PORT || 5000;

app.use(cors());
app.use(express.json());

mongoose.connect('mongodb://localhost:27017/tendzzy-store', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
});

const productSchema = new mongoose.Schema({
    name: String,
    description: String,
    price: Number,
});

const Product = mongoose.model('Product', productSchema);

app.get('/api/products', async (req, res) => {
    const products = await Product.find();
    res.json(products);
});

app.post('/api/products', async (req, res) => {
    const newProduct = new Product(req.body);
    await newProduct.save();
    res.json(newProduct);
});

app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
