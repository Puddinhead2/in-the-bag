# Grab Bag Feature Plan

## Step 1: Design the Grab Bag Functionality

### Criteria for Items to be Placed in the Grab Bag
- Items that need to be cleared from inventory can be placed in the Grab Bag.
- These items could be identified based on factors like age in inventory, low sales, or manual selection.

### Assigning Numbers to Items
- Each item added to the Grab Bag will be assigned a number from 1 to 10.
- This can be managed using a simple counter that increments with each item added.

### Logic to Check if the Grab Bag is Full
- The Grab Bag is considered full when it contains 10 items.
- We can use a counter to track the number of items in the Grab Bag.

## Step 2: Implement the Grab Bag Feature

### Backend Function to Handle Item Clearance and Assign Numbers
1. **API Endpoint**: Create an API endpoint to manage clearance items and assign numbers.
   - Example: `/api/clearance-items`

2. **Database Schema**: Update your database schema to include a `clearance` flag and a `grab_bag_number` field for items.

### Frontend Interface for the Grab Bag
1. **Grab Bag UI**: Create a user interface for the Grab Bag on your e-commerce site.
   - Example: A section on the product page or a dedicated Grab Bag page where users can see the items added to the Grab Bag.

2. **Add to Grab Bag**: Provide an option for users to add items to the Grab Bag.

### Functionality to Place the Full Grab Bag into the Shopping Cart
1. **Add to Cart**: Once the Grab Bag is full (contains 10 items), provide an option for users to add the entire Grab Bag to their shopping cart.

## Follow-up Steps

1. **Testing**: Ensure that the Grab Bag feature works as intended by thoroughly testing it.
2. **User Feedback**: Collect feedback from users to improve the feature.

# Example Code

## Backend (Node.js/Express)
```javascript name=grab-bag-backend.js
const express = require('express');
const app = express();
app.use(express.json());

let grabBag = [];
const maxItems = 10;

app.post('/api/clearance-items', (req, res) => {
  const item = req.body;
  if (grabBag.length < maxItems) {
    item.grabBagNumber = grabBag.length + 1;
    grabBag.push(item);
    res.status(201).send(item);
  } else {
    res.status(400).send({ message: 'Grab Bag is full' });
  }
});

app.get('/api/clearance-items', (req, res) => {
  res.send(grabBag);
});

app.post('/api/checkout', (req, res) => {
  if (grabBag.length === maxItems) {
    // Logic to add Grab Bag to shopping cart
    res.send({ message: 'Grab Bag added to shopping cart' });
  } else {
    res.status(400).send({ message: 'Grab Bag is not full' });
  }
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

## Frontend (HTML/JavaScript)
```html name=grab-bag-frontend.html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Grab Bag</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      background-color: #f5f5f5;
      margin: 0;
    }
    .grab-bag {
      width: 300px;
      height: 400px;
      background-color: #f8d5b7;
      border: 2px solid #d1a17b;
      border-radius: 5px;
      box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
      position: relative;
      overflow: hidden;
    }
    .grab-bag::before, .grab-bag::after {
      content: "";
      position: absolute;
      width: 90%;
      height: 20px;
      background-color: #f8d5b7;
      border: 2px solid #d1a17b;
      top: -10px;
      left: 5%;
      border-radius: 50%;
    }
    .grab-bag::after {
      top: -30px;
      left: 10%;
      width: 80%;
      height: 20px;
    }
    .grab-bag-title {
      text-align: center;
      font-size: 24px;
      color: #333;
      margin: 20px 0;
    }
    .grab-bag-items {
      list-style: none;
      padding: 0;
      margin: 0 10px;
      height: calc(100% - 100px);
      overflow-y: auto;
    }
    .grab-bag-item {
      background-color: #fff;
      border: 1px solid #d1a17b;
      border-radius: 3px;
      margin: 10px 0;
      padding: 10px;
      box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    }
    .checkout-button {
      display: block;
      width: 80%;
      margin: 20px auto;
      padding: 10px;
      background-color: #d1a17b;
      color: white;
      text-align: center;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    .notification {
      text-align: center;
      color: red;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <div class="grab-bag">
    <div class="grab-bag-title">Grab Bag</div>
    <ul class="grab-bag-items" id="grabBagList"></ul>
    <div class="notification" id="notification"></div>
    <button class="checkout-button" id="checkoutButton" onclick="checkoutGrabBag()">Checkout Grab Bag</button>
  </div>

  <script>
    async function loadGrabBag() {
      const response = await fetch('/api/clearance-items');
      const grabBag = await response.json();
      const grabBagList = document.getElementById('grabBagList');
      const notification = document.getElementById('notification');
      grabBagList.innerHTML = '';
      grabBag.forEach(item => {
        const listItem = document.createElement('li');
        listItem.className = 'grab-bag-item';
        listItem.textContent = `Item ${item.grabBagNumber}: ${item.name}`;
        grabBagList.appendChild(listItem);
      });
      if (grabBag.length === 10) {
        notification.textContent = 'The Grab Bag is full!';
      } else {
        notification.textContent = '';
      }
    }

    async function checkoutGrabBag() {
      const response = await fetch('/api/checkout', { method: 'POST' });
      const result = await response.json();
      alert(result.message);
    }

    loadGrabBag();
  </script>
</body>
</html>