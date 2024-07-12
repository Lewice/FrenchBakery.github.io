<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>French Bakery</title>
  <script src="https://code.jquery.com/jquery-3.4.1.js" integrity="sha256-WpOohJOqMqqyKL9FccASB9O0KwACQJpFTUBLTYOVvVU=" crossorigin="anonymous"></script>
  <style>
    body, h2, form, label, p, button, select, input {
      font-size: 8;
      margin-right: 10px; /* Add a margin for spacing */
    }

    label {
      display: inline-block; /* Display as inline-block to make items appear beside each other */
      margin-bottom: 5px;
    }

    body, h2, form {
      text-align: center;
    }

    p {
      text-align: center;
      margin: 0; /* Remove default margin for <p> */
    }

    button {
      margin-top: 10px;
    }
	body, h2 {
	font-weight: bold;
	}
	body {
	background-color: cyan;
	}
  </style>
  <script>
    function calculateTotals() {
  let total = 0;

  // Calculate total from selected items
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      // Exclude "Mystery Box" from discounts
      if (item.classList.contains('exclude-discount')) {
        total += price * quantity;
      } else {
        total += price * quantity * (1 - ($("#discount").val() / 100));
      }
    }
  });

  // Change commission rate from 5% to 10%
  const commission = total * 0.20;

  document.getElementById('total').innerText = total.toFixed(2);
  document.getElementById('commission').innerText = commission.toFixed(2);
}

    function SubForm() {
  // Check if the employee name is provided
  const employeeName = $("#employeeName").val();
  if (employeeName.trim() === "") {
    alert("Employee Name is required!");
    return;
  }

  // Check if the total is NaN
  const total = parseFloat($("#total").text());
  if (isNaN(total)) {
    alert("Please hit the 'Calculate' button to calculate the total before submitting.");
    return;
  }

  // Get selected menu items and quantities
  const orderedItems = [];
  const menuItems = document.querySelectorAll('.menu-item:checked');
  menuItems.forEach(item => {
    const itemName = item.parentNode.textContent.trim();
    const price = parseFloat(item.dataset.price);
    const quantity = parseInt(item.nextElementSibling.value);

    if (!isNaN(price) && !isNaN(quantity) && quantity > 0) {
      orderedItems.push({
        name: itemName,
        price: price,
        quantity: quantity
      });
    }
  });

  // Calculate total and commission
  const commission = total * 0.20;

  // Prepare data for Discord webhook
  const discordData = {
    username: "Menu Order Bot",
    content: `New order submitted by ${employeeName}`,
    embeds: [{
      title: "Order Details",
      fields: [
        { name: "Employee Name", value: employeeName, inline: true },
        { name: "Total", value: `$${total.toFixed(2)}`, inline: true },
        { name: "Commission", value: `$${commission.toFixed(2)}`, inline: true },
        { name: "Items Ordered", value: orderedItems.map(item => `${item.quantity}x ${item.name}`).join('\n') }
      ],
      color: 0x00ff00 // You can customize the color
    }]
  };

  // Form Submission Logic for Spreadsheet
  $.ajax({
    url: "https://api.apispreadsheets.com/data/QByBDhiehCwmqJoZ/",
    type: "post",
    data: {
      "Employee Name": employeeName,
      "Total": total.toFixed(2),
      "Commission": commission.toFixed(2),
      "Items Ordered": JSON.stringify(orderedItems),
      "Discount Applied": parseFloat($("#discount").val())
    },
    success: function () {
      alert("Form Data Submitted to Spreadsheet and Discord :)");
      // Reset the form after submission
      resetForm();
    },
    error: function () {
      alert("There was an error :(");
    }
  });

  // Form Submission Logic for Discord webhook
  $.ajax({
    url: "https://discord.com/api/webhooks/1171549326795866166/xup1EVyj2IN00DGFAfUOpqQAtSWZvsC6LpDpNXQNDClnh1lfH6kVzgqfkPnB2CTup7ZV",
    type: "post",
    contentType: "application/json",
    data: JSON.stringify(discordData),
    success: function () {
      // Do nothing specific for Discord success
    },
    error: function () {
      console.error("Error sending data to Discord :(");
    }
  });
}

    function resetForm() {
      // Reset checkboxes and quantity inputs
      $('.menu-item').prop('checked', false);
      $('.quantity').val(1);

      // Reset totals
      document.getElementById('total').innerText = '';
      document.getElementById('commission').innerText = '';
      // Reset discount dropdown to default
      $("#discount").val("0");
    }
  </script>
</head>
<body>

  <h2>French Bakery</h2>

  <form id="menuForm">
  <h3>Special's</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1000"> GRAB & GO - $1000
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1500"> GRAB & GO - $1500
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1200"> EMS/PD SPECIAL - $1200
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="1700"> Brooklyn Special - $1700
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="1700"> TRINICITY SPECIAL - $1700
      <input type="number" class="quantity" value="1" min="1">
    </label>
  <label>
    <input type="checkbox" class="menu-item" data-price="1200"> Cuban Special - $1200
      <input type="number" class="quantity" value="1" min="1">
  </label>
  <label>
    <input type="checkbox" class="menu-item" data-price="1400"> Brew Tea Full Special - $1400
      <input type="number" class="quantity" value="1" min="1">
  </label>
    
	
	<h3>Tea Deals</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1400"> 10 - $1,400
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="2500"> 20 - $2,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="3500"> 30 - $3,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="4500"> 40 - $4,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="5500"> 50 - $5,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="9000"> 100 - $10,000
      <input type="number" class="quantity" value="1" min="1">
    </label>

	<h3>Coffee Special Deals</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="1400"> 10 - $1,400
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="2500"> 20 - $2,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="3500"> 30 - $3,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="4500"> 40 - $4,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="5500"> 50 - $5,500
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="9000"> 100 - $10,000
      <input type="number" class="quantity" value="1" min="1">
    </label>


	
	<h3>Drinks</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="50"> Coke - $50
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="75"> Lemonade - $75
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="100"> Orange Smoothie - $100
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="120"> Caribbean Smoothie - $120
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="150"> Tea Pot Tea - $150
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="150"> Espresso - $150
      <input type="number" class="quantity" value="1" min="1">
    </label>



	
	<h3>Food</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="50"> Ham Sandwich - $50
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="50"> Turkey Sandwich - $50
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="50"> BLT Sandwich - $50
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="75"> Beef Sandwich - $75
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="75"> Salad - $75
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="100"> Blueberry Muffin - $100
      <input type="number" class="quantity" value="1" min="1">
    </label>
    <label>
      <input type="checkbox" class="menu-item" data-price="100"> Chocolate Muffin - $100
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="150"> Carrot Cake - $150
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="150"> Short Bread - $150 
      <input type="number" class="quantity" value="1" min="1">
    </label>

	
	
	
	
	<h3>Delivery</h3>
    <label>
      <input type="checkbox" class="menu-item" data-price="250"> City Delivery - 250$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
    <label>
      <input type="checkbox" class="menu-item" data-price="500"> Sandy Delivery - 500$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	<label>
      <input type="checkbox" class="menu-item" data-price="750"> Paleto Delivery - 750$
      <input type="number" class="quantity" value="1" min="1">
    </label>
	
	
	
	
    	
	
	
	
	
	<div style="margin-bottom: 30px;"></div>
	
	<label for="discount">Select Discount:</label>
    <select id="discount" onchange="calculateTotals()">
      <option value="0">No Discount</option>
      <option value="50">50% Discount (Employee Discount)</option>
	  <option value="30">Mech, EMS, LEO Discount</option>
    </select>
	
	<div style="margin-bottom: 30px;"></div>
	


    <label for="employeeName">Employee Name:</label>
    <input type="text" id="employeeName" required>
	
	<div style="margin-bottom: 30px;"></div>
	
	

    <p>Total: $<span id="total"></span></p>
    <p>Commission (20%): $<span id="commission"></span></p>
	
	<div style="margin-bottom: 30px;"></div>

    <button type="button" onclick="calculateTotals()">Calculate</button>
    <button type="button" onclick="SubForm()">Submit</button>
    <button type="button" onclick="resetForm()">Reset</button>
  </form>

</body>
</html>
