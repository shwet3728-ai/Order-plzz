<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Shwet's Café – QR Ordering</title>
  <style>
    * { box-sizing: border-box; }

    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      margin: 0;
      padding: 0;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(135deg, #ff9a9e 0%, #fad0c4 40%, #fbc2eb 100%);
    }

    .container {
      width: 100%;
      max-width: 650px;
      margin: 20px;
      background: #ffffffee;
      backdrop-filter: blur(8px);
      border-radius: 18px;
      box-shadow: 0 18px 45px rgba(0, 0, 0, 0.18);
      padding: 22px 20px 24px;
    }

    .header {
      text-align: center;
      margin-bottom: 18px;
    }

    .shop-name {
      font-size: 26px;
      font-weight: 800;
      letter-spacing: 0.04em;
      text-transform: uppercase;
      color: #ff5252;
    }

    .tagline {
      font-size: 14px;
      color: #666;
      margin-top: 4px;
    }

    h2 {
      text-align: center;
      font-size: 18px;
      margin: 16px 0 10px;
      color: #333;
    }

    .menu-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      margin-bottom: 12px;
      padding: 10px 12px;
      border-radius: 10px;
      background: #fafafa;
      border: 1px solid #f0f0f0;
    }

    .menu-item-info {
      display: flex;
      flex-direction: column;
    }

    .menu-item-name {
      font-weight: 600;
      font-size: 15px;
      color: #333;
    }

    .menu-item-price {
      font-size: 13px;
      color: #777;
      margin-top: 2px;
    }

    .qty-input {
      width: 70px;
      padding: 6px;
      border-radius: 8px;
      border: 1px solid #ddd;
      font-size: 14px;
      text-align: center;
    }

    .qty-input:focus {
      outline: none;
      border-color: #ff6f61;
      box-shadow: 0 0 0 2px rgba(255, 111, 97, 0.25);
    }

    .field-group {
      margin-top: 14px;
      display: grid;
      grid-template-columns: 1fr;
      gap: 10px;
    }

    label {
      display: block;
      margin-bottom: 4px;
      font-size: 13px;
      color: #555;
    }

    input[type="text"],
    input[type="tel"] {
      width: 100%;
      padding: 8px 10px;
      border-radius: 8px;
      border: 1px solid #ddd;
      font-size: 14px;
    }

    input[type="text"]:focus,
    input[type="tel"]:focus {
      outline: none;
      border-color: #ff6f61;
      box-shadow: 0 0 0 2px rgba(255, 111, 97, 0.25);
    }

    #total {
      font-weight: 700;
      text-align: right;
      margin-top: 12px;
      font-size: 16px;
      color: #333;
    }

    button {
      width: 100%;
      padding: 12px;
      border: none;
      border-radius: 999px;
      cursor: pointer;
      font-size: 16px;
      font-weight: 600;
      margin-top: 18px;
      transition: transform 0.08s ease, box-shadow 0.08s ease, background 0.15s;
    }

    #placeOrderBtn {
      background: linear-gradient(135deg, #ff6f61, #ff3d7f);
      color: #fff;
      box-shadow: 0 10px 20px rgba(255, 63, 111, 0.35);
    }

    #placeOrderBtn:hover:not(:disabled) {
      transform: translateY(-1px);
      box-shadow: 0 14px 26px rgba(255, 63, 111, 0.45);
    }

    #placeOrderBtn:active:not(:disabled) {
      transform: translateY(0);
      box-shadow: 0 8px 18px rgba(255, 63, 111, 0.35);
    }

    #placeOrderBtn:disabled {
      background: #ccc;
      box-shadow: none;
      cursor: not-allowed;
    }

    #message {
      margin-top: 10px;
      text-align: center;
      font-size: 13px;
      color: #444;
    }

    .subtext-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-top: 6px;
      font-size: 12px;
      color: #777;
    }

    .pill {
      display: inline-flex;
      align-items: center;
      gap: 6px;
      padding: 4px 10px;
      border-radius: 999px;
      background: #ffe3e0;
      font-size: 11px;
      color: #c0392b;
    }

    .pill-dot {
      width: 6px;
      height: 6px;
      border-radius: 50%;
      background: #e74c3c;
    }
  </style>
</head>

<body>
  <div class="container">
    <div class="header">
      <div class="shop-name">Shwet's Café</div>
      <div class="tagline">Fresh food · Fast service · Smart payments</div>
    </div>

    <h2>Your Order</h2>
    <div id="menu">
      <!-- Edit / add your items here -->
      <div class="menu-item">
        <div class="menu-item-info">
          <span class="menu-item-name">Margherita Pizza</span>
          <span class="menu-item-price">₹299 · Classic cheese & tomato</span>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Margherita Pizza" data-price="299">
      </div>

      <div class="menu-item">
        <div class="menu-item-info">
          <span class="menu-item-name">Veg Burger</span>
          <span class="menu-item-price">₹149 · Crispy veg patty with fries</span>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Veg Burger" data-price="149">
      </div>

      <div class="menu-item">
        <div class="menu-item-info">
          <span class="menu-item-name">Cold Coffee</span>
          <span class="menu-item-price">₹99 · Chilled & creamy</span>
        </div>
        <input type="number" class="qty-input" min="0" value="0"
               data-name="Cold Coffee" data-price="99">
      </div>
      <!-- Copy this block to add more items -->
    </div>

    <div id="total">Total: ₹0</div>

    <h2>Customer Details</h2>
    <div class="field-group">
      <div>
        <label for="name">Name*</label>
        <input type="text" id="name" required>
      </div>
      <div>
        <label for="phone">Mobile Number*</label>
        <input type="tel" id="phone" required>
      </div>
      <div>
        <label for="tableNo">Table No (optional)</label>
        <input type="text" id="tableNo">
      </div>
    </div>

    <button id="placeOrderBtn">Place Order & Pay</button>

    <div class="subtext-row">
      <span>UPI payment supported: PhonePe, GPay, Paytm, BHIM</span>
      <span class="pill"><span class="pill-dot"></span>Secure UPI</span>
    </div>

    <div id="message"></div>

    <!-- Hidden link we "click" to trigger UPI -->
    <a id="upiLink" href="#" style="display:none;">Pay</a>
  </div>

  <script>
    // 👉 Change this name if you rename the shop
    const SHOP_NAME = "Shwet's Café";

    // ✅ Your Google Apps Script Web App URL
    const APPS_SCRIPT_URL =
      "https://script.google.com/macros/s/AKfycbxq3AH_jic5wRcUg0AjD2zsk2RPZgM1QVh1PZBBY24/exec";

    // ✅ Your UPI details
    const UPI_ID = "9431505374-2@axl";
    const UPI_NAME = SHOP_NAME;

    const qtyInputs = document.querySelectorAll('.qty-input');
    const totalDiv = document.getElementById('total');
    const placeOrderBtn = document.getElementById('placeOrderBtn');
    const messageDiv = document.getElementById('message');
    const upiLink = document.getElementById('upiLink');

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

      // 3) Try to open UPI app
      if (!isMobile()) {
        // On non-mobile devices, just show a simple message (no UPI URL text)
        messageDiv.textContent = "UPI payment works on mobile devices with a UPI app installed.";
        return;
      }

      messageDiv.textContent = "Opening your UPI app for payment...";
      placeOrderBtn.disabled = true;

      // Hidden link click + fallback
      upiLink.href = upiUrl;
      upiLink.click();
      setTimeout(() => {
        window.location.href = upiUrl;
      }, 200);
    });
  </script>
</body>
</html>
