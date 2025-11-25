<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Restaurant QR Menu</title>
  <style>
    * { box-sizing: border-box; }
    body {
      font-family: Arial, sans-serif;
      background: #f5f5f5;
      margin: 0;
      padding: 20px;
    }
    .container {
      max-width: 600px;
      margin: 0 auto;
      background: #ffffff;
      padding: 20px;
      border-radius: 10px;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
    }
    h1, h2, h3 {
      text-align: center;
      margin-top: 0;
    }
    .menu-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 12px;
      padding-bottom: 8px;
      border-bottom: 1px solid #eee;
    }
    .menu-item-name { font-weight: 600; }
    .qty-input { width: 60px; padding: 4px; }
    .field { margin-bottom: 12px; }
    label {
      display: block;
      margin-bottom: 4px;
      font-size: 14px;
    }
    input {
      width: 100%;
      padding: 8px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-size: 14px;
    }
    #total {
      font-weight: bold;
      text-align: right;
      margin-top: 10px;
      font-size: 16px;
    }
    button {
      width: 100%;
      padding: 10px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
    }
    #placeOrderBtn {
      background: #28a745;
      color: #fff;
      margin-top: 10px;
    }
    #placeOrderBtn:disabled {
      background: #aaa;
      cursor: not-allowed;
    }
    #message {
      margin-top: 10px;
      text-align: center;
      font-size: 14px;
    }
    .note {
      font-size: 12px;
      color: #555;
      margin-top: 6px;
      text-align: center;
    }
    #upiUrlDisplay {
      font-size: 11px;
      word-wrap: break-word;
      margin-top: 6px;
      color: #333;
      text-align: center;
    }
  </style>
</head>

<body>
  <div class="container">
    <h1>Welcome to Our Restaurant</h1>
    <h2>Scan · Order · Pay</h2>

    <h3>Menu</h3>
    <div id="menu">
      <!-- 🔽 Edit / add your items here -->
      <div class="menu-item">
        <div>
          <div class="menu-item-name">Margherita Pizza</div>
          <div>₹<span class="price">299</span></div>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Margherita Pizza" data-price="299">
      </div>

      <div class="menu-item">
        <div>
          <div class="menu-item-name">Veg Burger</div>
          <div>₹<span class="price">149</span></div>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Veg Burger" data-price="149">
      </div>

      <div class="menu-item">
        <div>
          <div class="menu-item-name">Cold Coffee</div>
          <div>₹<span class="price">99</span></div>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Cold Coffee" data-price="99">
      </div>
      <!-- 🔼 Copy this block to add more items -->
    </div>

    <div id="total">Total: ₹0</div>

    <h3>Your Details</h3>
    <div class="field">
      <label for="name">Name*</label>
      <input type="text" id="name" required>
    </div>

    <div class="field">
      <label for="phone">Mobile Number*</label>
      <input type="tel" id="phone" required>
    </div>

    <div class="field">
      <label for="tableNo">Table No (optional)</label>
      <input type="text" id="tableNo">
    </div>

    <button id="placeOrderBtn" disabled>Place Order & Pay</button>
    <div class="note">
      Use this page on your <b>mobile phone</b>. After clicking, your UPI app (PhonePe / GPay / Paytm) will open with the exact amount.
    </div>

    <!-- Hidden link we "click" to trigger UPI -->
    <a id="upiLink" href="#" style="display:none;">Pay</a>
    <div id="upiUrlDisplay"></div>

    <div id="message"></div>
  </div>

  <script>
    // ✅ Your Google Apps Script Web App URL
    const APPS_SCRIPT_URL =
      "https://script.google.com/macros/s/AKfycbxq3AH_jic5wRcUg0AjD2zsk2RPZgM1QVh1PZBBY24/exec";

    // ✅ Your UPI details
    const UPI_ID = "9431505374-2@axl";
    const UPI_NAME = "Restaurant"; // change to your shop name if you want

    const qtyInputs = document.querySelectorAll('.qty-input');
    const totalDiv = document.getElementById('total');
    const placeOrderBtn = document.getElementById('placeOrderBtn');
    const messageDiv = document.getElementById('message');
    const upiLink = document.getElementById('upiLink');
    const upiUrlDisplay = document.getElementById('upiUrlDisplay');

    let totalAmount = 0;

    function calculateTotal() {
      totalAmount = 0;
      qtyInputs.forEach(input => {
        const qty = parseInt(input.value) || 0;
        const price = parseInt(input.dataset.price);
        totalAmount += qty * price;
      });

      totalDiv.textContent = 'Total: ₹' + totalAmount;

      const name = document.getElementById('name').value.trim();
      const phone = document.getElementById('phone').value.trim();
      placeOrderBtn.disabled = !(totalAmount > 0 && name && phone);
    }

    qtyInputs.forEach(input => input.addEventListener('input', calculateTotal));
    document.getElementById('name').addEventListener('input', calculateTotal);
    document.getElementById('phone').addEventListener('input', calculateTotal);

    function isMobile() {
      return /Android|iPhone|iPad|iPod/i.test(navigator.userAgent);
    }

    function buildUpiUrl(amount, customerName) {
      const params = new URLSearchParams({
        pa: UPI_ID,
        pn: UPI_NAME,
        am: amount.toString(),
        cu: "INR",
        tn: `Food order - ${customerName}`
      });
      return "upi://pay?" + params.toString();
    }

    placeOrderBtn.addEventListener('click', async () => {
      messageDiv.textContent = '';

      const name = document.getElementById('name').value.trim();
      const phone = document.getElementById('phone').value.trim();
      const tableNo = document.getElementById('tableNo').value.trim();

      // Build items array
      const items = [];
      qtyInputs.forEach(input => {
        const qty = parseInt(input.value) || 0;
        if (qty > 0) {
          items.push({
            name: input.dataset.name,
            price: parseInt(input.dataset.price),
            qty: qty
          });
        }
      });

      if (items.length === 0) {
        messageDiv.textContent = "Please select at least one item.";
        return;
      }

      // Prepare data for Google Sheet
      const orderData = {
        name,
        phone,
        tableNo,
        items,
        total: totalAmount,
        paymentStatus: "Pending"
      };

      // 1) Send order to Google Sheet
      try {
        await fetch(APPS_SCRIPT_URL, {
          method: "POST",
          mode: "no-cors",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify(orderData)
        });
      } catch (err) {
        console.error("Error sending order to Apps Script:", err);
      }

      // 2) Build UPI deep link with dynamic amount
      const upiUrl = buildUpiUrl(totalAmount, name);

      // Show for debugging / copy-paste fallback
      upiUrlDisplay.textContent =
        "If payment app does not open automatically, copy & paste this into your UPI app: " + upiUrl;

      // 3) Try to open UPI app
      if (!isMobile()) {
        alert("UPI payment link works only on a mobile phone with a UPI app installed.");
      }

      upiLink.href = upiUrl;
      // Try via hidden link click
      upiLink.click();
      // Also try via window.location as backup
      setTimeout(() => {
        window.location.href = upiUrl;
      }, 300);

      messageDiv.textContent = "Redirecting to your UPI app for payment...";
      placeOrderBtn.disabled = true;
    });
  </script>
</body>
</html>
