# movie
html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Novel Purchase App</title>
    <link rel="stylesheet" href="index.css">
</head>
<body>

    <h1>Novel Purchase App</h1>

    <div id="novel-list">
        <!-- Novels will be dynamically loaded here -->
    </div>

    <h2>Shopping Cart</h2>
    <div id="cart">
        <ul id="cart-items"></ul>
        <p>Total: $<span id="total">0.00</span></p>
        <button id="checkout">Checkout</button>
    </div>

    <script src="index.js"></script>
</body>
</html>




css
body {
    font-family: Arial, sans-serif;
    text-align: center;
    margin: 20px;
}

#novel-list, #cart {
    margin: 20px auto;
    padding: 10px;
    border: 1px solid #ddd;
    width: 300px;
}

button {
    margin-top: 10px;
    padding: 5px 10px;
    cursor: pointer;
}

#cart ul {
    list-style: none;
    padding: 0;
}

js


const cart = [];
const searchInput =document.getElementById('searchInput')


async function fetchBooks() {
    try {
        const response = await fetch("https://openlibrary.org/search.json?q=classic&limit=5");
        const data = await response.json();
        
        // Transform API response to match your format
        const books = data.docs.map((book, index) => ({
            id: index + 1, // Assign a unique ID
            title: book.title,
            price: (Math.random() * 20 + 5).toFixed(2) // Random price between $5 and $25
        }));

        displayNovels(books);
    } catch (error) {
        console.error("Error fetching books:", error);
    }
}

function displayNovels(novels) {
    const novelList = document.getElementById("novel-list");
    novelList.innerHTML = ""; // Clear previous list

    novels.forEach(novel => {
        const novelDiv = document.createElement("div");
        novelDiv.innerHTML = `
            <p>${novel.title} - $${novel.price}</p>
            <button onclick="addToCart(${novel.id}, '${novel.title}', ${novel.price})">Add to Cart</button>
        `;
        novelList.appendChild(novelDiv);
    });
}

function addToCart(id, title, price) {
    const novel = { id, title, price };
    cart.push(novel);
    updateCart();
}

function updateCart() {
    const cartItems = document.getElementById("cart-items");
    const totalElement = document.getElementById("total");

    cartItems.innerHTML = "";
    let total = 0;

    cart.forEach((novel, index) => {
        const li = document.createElement("li");
        li.innerHTML = `
            ${novel.title} - $${novel.price}
            <button onclick="removeFromCart(${index})">Remove</button>
        `;
        cartItems.appendChild(li);
        total += parseFloat(novel.price);
    });

    totalElement.textContent = total.toFixed(2);
}

function removeFromCart(index) {
    cart.splice(index, 1);
    updateCart();
}

document.getElementById("checkout").addEventListener("click", () => {
    if (cart.length === 0) {
        alert("Your cart is empty!");
    } else {
        alert("Thank you for your purchase!");
        cart.length = 0;
        updateCart();
    }
});

// Fetch books when the page loads
fetchBooks();

