<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Sriya Saraswathi Silks</title>
  <style>
    body { margin:0; font-family:Arial, sans-serif; }
    header { background:#800000; color:white; padding:15px; text-align:center; font-size:24px; font-weight:bold; }
    nav { display:flex; justify-content:space-between; background:#f2f2f2; padding:10px; }
    .nav-left, .nav-right { display:flex; gap:15px; }
    .nav-left a, .nav-right a { text-decoration:none; color:#333; font-weight:bold; cursor:pointer; }
    main { padding:20px; }
    .categories { display:grid; grid-template-columns:repeat(auto-fill,minmax(200px,1fr)); gap:15px; }
    .category, .product { border:1px solid #ccc; padding:10px; text-align:center; background:#fafafa; cursor:pointer; }
    .category:hover, .product:hover { background:#e6e6e6; }
    .product img { width:100%; height:150px; object-fit:cover; }
    .checkout { margin-top:20px; text-align:center; }
    .checkout button { padding:10px 20px; background:#800000; color:white; border:none; cursor:pointer; font-size:16px; }
  </style>
</head>
<body>

<header>Sriya Saraswathi Silks</header>

<nav>
  <div class="nav-left">
    <a onclick="goHome()">Home</a>
    <a onclick="aboutUs()">About Us</a>
    <a onclick="showCategories()">Categories</a>
    <a onclick="contactUs()">Contact Us</a>
  </div>
  <div class="nav-right">
    <a onclick="userLogin()">User Login</a>
    <a onclick="filterItems()">Filter</a>
    <a onclick="sortItems()">Sort</a>
    <a onclick="viewCart()">Cart</a>
  </div>
</nav>

<main id="content">
  <h2>Welcome to Sriya Saraswathi Silks</h2>
  <p>Explore our exquisite collection of silk sarees and ethnic wear.</p>
</main>

<script>
  let cart = [];

  function goHome() {
    document.getElementById('content').innerHTML = `
      <h2>Home</h2>
      <p>Welcome to Sriya Saraswathi Silks, your destination for authentic silk sarees and ethnic wear.</p>
    `;
  }

  function aboutUs() {
    document.getElementById('content').innerHTML = `
      <h2>About Us</h2>
      <p>Sriya Saraswathi Silks brings you the finest silk sarees from across India, blending tradition with elegance.</p>
    `;
  }

  async function showCategories() {
    const response = await fetch('products.json');
    const data = await response.json();
    let html = `<h2>Categories</h2><div class="categories">`;
    data.categories.forEach(cat => {
      html += `<div class="category" onclick="showProducts('${cat.name}')">${cat.name}</div>`;
    });
    html += `</div>`;
    document.getElementById('content').innerHTML = html;
  }

  async function showProducts(category) {
    const response = await fetch('products.json');
    const data = await response.json();
    const cat = data.categories.find(c => c.name === category);

    if (!cat || cat.products.length === 0) {
      document.getElementById('content').innerHTML = `<h2>${category}</h2><p>No products available yet.</p>`;
      return;
    }

    let html = `<h2>${category}</h2><div class="categories">`;
    cat.products.forEach(p => {
      html += `
        <div class="product">
          <img src="${p.image}" alt="${p.name}">
          <h3>${p.name}</h3>
          <p>₹${p.price}</p>
          <p>${p.description}</p>
          <button onclick="addToCart('${p.id}','${p.name}',${p.price})">Add to Cart</button>
        </div>`;
    });
    html += `</div>`;
    document.getElementById('content').innerHTML = html;
  }

  function contactUs() {
    document.getElementById('content').innerHTML = `
      <h2>Contact Us</h2>
      <p>Email: info@sriyasaraswathisilks.com</p>
      <p>Phone: +91-9876543210</p>
    `;
  }

  function userLogin() {
    document.getElementById('content').innerHTML = `
      <h2>User Login</h2>
      <form onsubmit="login(event)">
        <label>Username: <input type="text" id="username"></label><br><br>
        <label>Password: <input type="password" id="password"></label><br><br>
        <button type="submit">Login</button>
      </form>
    `;
  }

  function login(event) {
    event.preventDefault();
    const user = document.getElementById('username').value;
    alert("Welcome, " + user + "!");
    goHome();
  }

  function filterItems() { alert("Filter function activated."); }
  function sortItems() { alert("Sort function activated."); }

  function addToCart(id, name, price) {
    cart.push({id, name, price});
    alert(name + " added to cart!");
  }

  function viewCart() {
    if(cart.length === 0) {
      document.getElementById('content').innerHTML = `<h2>Your Cart</h2><p>No items in cart yet.</p>`;
      return;
    }
    let total = cart.reduce((sum,item)=>sum+item.price,0);
    let html = `<h2>Your Cart</h2><ul>`;
    cart.forEach(item => {
      html += `<li>${item.name} - ₹${item.price}</li>`;
    });
    html += `</ul><p><strong>Total: ₹${total}</strong></p>`;
    html += `<div class="checkout"><button onclick="proceedToCheckout()">Proceed to Checkout</button></div>`;
    document.getElementById('content').innerHTML = html;
  }

  function proceedToCheckout() {
    if(cart.length === 0) {
      alert("Your cart is empty!");
      return;
    }

    const order = {
      order_id: "ORD" + Math.floor(Math.random() * 10000),
      customer: {
        name: "Ramya",
        phone: "+91-9741578628",
        email: "ramya@example.com",
        address: "Hyderabad, Telangana, India"
      },
      items: cart.map(item => ({
        product_id: item.id,
        name: item.name,
        quantity: 1,
        unit_price: item.price,
        subtotal: item.price
      })),
      payment: {
        method: "Cash on Delivery",
        status: "Pending"
      },
      total_cost: cart.reduce((sum,item)=>sum+item.price,0),
      order_date: new Date().toISOString(),
      status: "Placed"
    };

    let orderDetails = order.items.map(i => `${i.name} - ₹${i.unit_price}`).join(", ");
    let message = encodeURIComponent(
      `Order ID: ${order.order_id}\nCustomer: ${order.customer.name}\nItems: ${orderDetails}\nTotal: ₹${order.total_cost}`
    );

    window.open(`https://wa.me/919741578628?text=${message}`, "_blank");

    console.log("Order JSON:", JSON.stringify(order, null, 2));
  }
</script>

</body>
</html>





{
  "categories": [
    {
      "name": "Pure Silk",
      "products": [
        {
          "id": "PS001",
          "name": "Elegant Pure Silk Saree",
          "description": "Handwoven pure silk saree with intricate zari work.",
          "price": 2500,
          "image": "images/pure_silk1.jpg"
        },
        {
          "id": "PS002",
          "name": "Classic Pure Silk Saree",
          "description": "Traditional design with rich silk texture.",
          "price": 3200,
          "image": "images/pure_silk2.jpg"
        }
      ]
    },
    {
      "name": "Kanchipuram Varieties",
      "products": [
        {
          "id": "KV001",
          "name": "Premium Kanchipuram Saree",
          "description": "Authentic Kanchipuram silk with





