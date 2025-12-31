<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Credit Card Fraud Detection System</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- Chart.js CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<style>
/* =======================
   GLOBAL STYLES
======================= */
*{
  box-sizing:border-box;
  font-family:Arial, Helvetica, sans-serif;
}

body{
  margin:0;
  background:#f3f4f6;
}

/* =======================
   CONTAINER
======================= */
.container{
  max-width:460px;
  margin:70px auto;
  background:#ffffff;
  padding:30px;
  border-radius:14px;
  box-shadow:0 20px 40px rgba(0,0,0,0.15);
  text-align:center;
}

h1,h2,h3{
  margin-bottom:18px;
}

input,select{
  width:100%;
  padding:12px;
  margin-bottom:14px;
  border:1px solid #ccc;
  border-radius:6px;
  font-size:15px;
}

button{
  width:100%;
  padding:12px;
  background:#2563eb;
  color:white;
  border:none;
  border-radius:6px;
  font-size:16px;
  cursor:pointer;
}

button:hover{
  background:#1e40af;
}

.hidden{
  display:none;
}

.safe{
  color:#15803d;
  font-weight:bold;
  font-size:18px;
}

.fraud{
  color:#b91c1c;
  font-weight:bold;
  font-size:18px;
}

.note{
  font-size:13px;
  color:#555;
  margin-top:12px;
}

canvas{
  margin-top:20px;
}
</style>
</head>

<body>

<!-- =======================
     LOGIN SECTION
======================= -->
<div class="container" id="loginBox">
  <h1>💳 Credit Card Fraud Detection</h1>

  <input type="email" id="email" placeholder="Email">
  <input type="password" id="password" placeholder="Password">

  <button onclick="login()">Login</button>

  <div class="note">
    Demo Credentials<br>
    Email: <b>admin@test.com</b><br>
    Password: <b>123456</b>
  </div>
</div>

<!-- =======================
   FRAUD DETECTION SECTION
======================= -->
<div class="container hidden" id="fraudBox">
  <h2>Transaction Details</h2>

  <input type="number" id="amount" placeholder="Transaction Amount (₹)">

  <select id="location">
    <option value="">Select Location</option>
    <option value="0">Known Location</option>
    <option value="1">Unknown Location</option>
  </select>

  <select id="device">
    <option value="">Select Device</option>
    <option value="0">Mobile</option>
    <option value="1">Emulator</option>
  </select>

  <button onclick="detectFraud()">Detect Fraud</button>

  <p id="result"></p>
  <p id="confidence"></p>

  <canvas id="fraudChart"></canvas>
</div>

<script>
/* =======================
   JAVASCRIPT LOGIC
======================= */

/* Demo Login Credentials */
const DEMO_EMAIL = "admin@test.com";
const DEMO_PASSWORD = "123456";

let fraudChart = null;

/* LOGIN FUNCTION */
function login(){
  const email = document.getElementById("email").value;
  const password = document.getElementById("password").value;

  if(email === DEMO_EMAIL && password === DEMO_PASSWORD){
    document.getElementById("loginBox").classList.add("hidden");
    document.getElementById("fraudBox").classList.remove("hidden");
  } else {
    alert("Invalid email or password");
  }
}

/*
 FRAUD DETECTION LOGIC
 ---------------------
 Mirrors Python ML logic:

 if amount > 50000 → fraud
 if location == 1 → fraud
 if device == 1 → fraud

 Confidence is calculated using weighted risk scoring.
*/

function detectFraud(){
  const amount = Number(document.getElementById("amount").value);
  const location = Number(document.getElementById("location").value);
  const device = Number(document.getElementById("device").value);

  let riskScore = 0;

  if(amount > 50000) riskScore += 40;
  if(location === 1) riskScore += 30;
  if(device === 1) riskScore += 30;

  const isFraud = riskScore >= 50;
  const confidence = isFraud ? riskScore : (100 - riskScore);

  const resultEl = document.getElementById("result");
  const confidenceEl = document.getElementById("confidence");

  if(isFraud){
    resultEl.innerText = "⚠️ Fraudulent Transaction Detected";
    resultEl.className = "fraud";
  } else {
    resultEl.innerText = "✅ Transaction is Safe";
    resultEl.className = "safe";
  }

  confidenceEl.innerText = "Prediction Confidence: " + confidence + "%";

  drawChart(riskScore, 100 - riskScore);
}

/* CHART FUNCTION */
function drawChart(fraudRisk, safeScore){
  const ctx = document.getElementById("fraudChart");

  if(fraudChart){
    fraudChart.destroy();
  }

  fraudChart = new Chart(ctx, {
    type: "doughnut",
    data: {
      labels: ["Fraud Risk", "Safe Score"],
      datasets: [{
        data: [fraudRisk, safeScore],
        backgroundColor: ["#dc2626", "#16a34a"]
      }]
    },
    options: {
      responsive: true,
      plugins: {
        legend: {
          position: "bottom"
        }
      }
    }
  });
}
</script>

</body>
</html>
