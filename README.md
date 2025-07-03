# Student-Exam-Form
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Semester Exam Registration</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f4f8;
      padding: 30px;
    }

    .container {
      max-width: 650px;
      margin: auto;
      background: white;
      padding: 25px;
      border-radius: 10px;
      box-shadow: 0 0 15px rgba(0,0,0,0.1);
    }

    h2 {
      text-align: center;
      margin-bottom: 20px;
      color: #2c3e50;
    }

    label {
      display: block;
      margin-bottom: 5px;
      font-weight: bold;
    }

    input, select {
      width: 100%;
      padding: 10px;
      margin-bottom: 15px;
      border-radius: 5px;
      border: 1px solid #ccc;
    }

    button {
      background: #2ecc71;
      color: white;
      padding: 12px 20px;
      border: none;
      border-radius: 5px;
      width: 100%;
      font-size: 16px;
      cursor: pointer;
    }

    button:hover {
      background: #27ae60;
    }

    .section-title {
      margin-top: 30px;
      margin-bottom: 10px;
      font-size: 18px;
      color: #34495e;
    }

    #qrArea, #cardSection, #confirmationBox {
      display: none;
      margin-top: 15px;
    }

    #qrArea img {
      width: 300px;
      display: block;
      margin: auto;
      border-radius: 10px;
      margin-top: 10px;
    }

    #timer {
      font-size: 20px;
      font-weight: bold;
      color: #e74c3c;
      text-align: center;
      margin-bottom: 15px;
    }

    #successMessage {
      display: none;
      font-size: 20px;
      color: #2ecc71;
      font-weight: bold;
      text-align: center;
      animation: pop 0.5s ease-in-out forwards;
    }

    @keyframes pop {
      0% { transform: scale(0.7); opacity: 0; }
      100% { transform: scale(1); opacity: 1; }
    }

    .radio-group {
      display: flex;
      gap: 20px;
      margin-bottom: 15px;
    }

    .radio-group label {
      font-weight: normal;
    }
  </style>
</head>
<body>

<div class="container">
  <h2>Semester Exam Registration</h2>
  <form id="examForm">
    <!-- Student Info -->
    <div class="section-title">Student Information</div>
    <label for="name">Full Name:</label>
    <input type="text" id="name" name="name" required>

    <label for="email">Email ID:</label>
    <input type="email" id="email" name="email" required>

    <label for="phone">Phone Number:</label>
    <input type="tel" id="phone" name="phone" required>

    <label for="roll">Roll Number:</label>
    <input type="text" id="roll" name="roll" required>

    <label for="dob">Date of Birth:</label>
    <input type="date" id="dob" name="dob" required>

    <!-- Exam Details -->
    <div class="section-title">Exam Details</div>
    <label for="exam">Select Exam:</label>
    <select id="exam" name="exam" required>
      <option value="">--Select--</option>
      <option value="semester">Semester Exam</option>
    </select>

    <!-- Payment Method -->
    <div class="section-title">Select Payment Method</div>
    <div class="radio-group">
      <label><input type="radio" name="paymentType" value="upi" onclick="togglePaymentSection()"> UPI</label>
      <label><input type="radio" name="paymentType" value="card" onclick="togglePaymentSection()"> Debit/Credit Card</label>
    </div>

    <!-- UPI Section -->
    <div id="qrSection">
      <label for="upiApp">Select UPI App:</label>
      <select id="upiApp" onchange="showUpiId()" required>
        <option value="">--Choose--</option>
        <option value="gpay">Google Pay</option>
        <option value="phonepe">PhonePe</option>
        <option value="paytm">Paytm</option>
      </select>

      <label for="upiIdInput">Enter or Edit UPI ID:</label>
      <input type="text" id="upiIdInput" placeholder="e.g. yourname@okaxis" required>

      <button type="button" onclick="startUpiPayment()">Show QR</button>

      <div id="qrArea">
        <div id="timer">Time left: 02:00</div>
        <img src="phonepe_qr.png" alt="QR Code">
        <div id="successMessage">✅ Payment Done Successfully!</div>
      </div>
    </div>

    <!-- Card Section -->
    <div id="cardSection">
      <label for="cardNumber">Card Number:</label>
      <input type="text" id="cardNumber" name="cardNumber" placeholder="1234 5678 9012 3456">

      <label for="expiry">Expiry Date:</label>
      <input type="month" id="expiry" name="expiry">

      <label for="cvv">CVV:</label>
      <input type="password" id="cvv" name="cvv" maxlength="3">
    </div>

    <!-- Confirmation Message & Download -->
    <div id="confirmationBox">
      <p style="font-size: 18px; color: green;">✅ Payment Successful! Your exam has been registered.</p>
      <button type="button" onclick="downloadReceipt()">📄 Download Receipt</button>
    </div>

    <button type="button" onclick="handlePayment()">Register & Pay</button>
  </form>
</div>

<script>
  let timerInterval;

  function togglePaymentSection() {
    const upi = document.querySelector('input[value="upi"]').checked;
    const card = document.querySelector('input[value="card"]').checked;

    document.getElementById("qrSection").style.display = upi ? "block" : "none";
    document.getElementById("cardSection").style.display = card ? "block" : "none";
    document.getElementById("confirmationBox").style.display = "none";
    document.getElementById("successMessage").style.display = "none";
    document.getElementById("timer").style.display = upi ? "block" : "none";
    document.getElementById("qrArea").style.display = "none";
    clearInterval(timerInterval);
  }

  function showUpiId() {
    const app = document.getElementById("upiApp").value;
    const upiInput = document.getElementById("upiIdInput");

    if (app === "gpay") upiInput.value = "yourname@okaxis";
    else if (app === "phonepe") upiInput.value = "yourname@ybl";
    else if (app === "paytm") upiInput.value = "yourname@paytm";
    else upiInput.value = "";
  }

  function startUpiPayment() {
    const app = document.getElementById("upiApp").value;
    if (!app) {
      alert("Please select a UPI app first.");
      return;
    }

    document.getElementById("qrArea").style.display = "block";
    startTimer(120);
  }

  function startTimer(duration) {
    let time = duration;
    const timerDisplay = document.getElementById("timer");
    const qrImage = document.querySelector("#qrArea img");
    const successMsg = document.getElementById("successMessage");

    clearInterval(timerInterval);
    timerInterval = setInterval(() => {
      const minutes = String(Math.floor(time / 60)).padStart(2, '0');
      const seconds = String(time % 60).padStart(2, '0');
      timerDisplay.textContent = `Time left: ${minutes}:${seconds}`;

      if (time <= 0) {
        clearInterval(timerInterval);
        timerDisplay.style.display = 'none';
        qrImage.style.display = 'none';
        successMsg.style.display = 'block';
        document.getElementById("confirmationBox").style.display = 'block';
      }
      time--;
    }, 1000);
  }

  function handlePayment() {
    const form = document.getElementById("examForm");
    if (!form.checkValidity()) {
      form.reportValidity();
      return;
    }

    const selectedMethod = document.querySelector('input[name="paymentType"]:checked');
    if (!selectedMethod) {
      alert("Please select a payment method.");
      return;
    }

    if (selectedMethod.value === "card") {
      document.getElementById("confirmationBox").style.display = "block";
    }
  }

  function downloadReceipt() {
    const name = document.getElementById("name").value;
    const roll = document.getElementById("roll").value;
    const exam = document.getElementById("exam").value;
    const paymentMethod = document.querySelector('input[name="paymentType"]:checked').value;
    const upiId = document.getElementById("upiIdInput").value;

    const receiptContent =
      `----- Exam Registration Receipt -----\n` +
      `Name           : ${name}\n` +
      `Roll Number    : ${roll}\n` +
      `Exam Type      : ${exam}\n` +
      `Payment Method : ${paymentMethod.toUpperCase()}\n` +
      (paymentMethod === 'upi' ? `UPI ID         : ${upiId}\n` : '') +
      `Status         : Payment Successful ✅\n` +
      `-------------------------------------\n`;

    const blob = new Blob([receiptContent], { type: "text/plain" });
    const link = document.createElement("a");
    link.href = URL.createObjectURL(blob);
    link.download = "receipt.txt";
    link.click();
  }
</script>
</body>
</html>
