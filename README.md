<!DOCTYPE html>
<html lang="th">
<head>
  <meta charset="UTF-8">
  <title>เฮียนุโซน - ร้านไก่ตัน Blox Fruits</title>
  <style>
    body {
      font-family: Tahoma, sans-serif;
      background-color: #111;
      color: #fff;
      margin: 0;
      padding: 0;
      text-align: center;
    }
    header {
      background: #000;
      padding: 15px;
      font-size: 24px;
      font-weight: bold;
      color: red;
    }
    footer {
      margin-top: 30px;
      padding: 10px;
      background: #000;
      font-size: 14px;
      color: gray;
    }
    .container {
      padding: 20px;
    }
    .product {
      background: #222;
      margin: 15px auto;
      padding: 20px;
      border-radius: 10px;
      max-width: 300px;
      box-shadow: 0 0 10px rgba(255,0,0,0.5);
    }
    .product h3 {
      margin: 0 0 10px;
      color: #ff4444;
    }
    .buy-btn {
      margin-top: 10px;
      padding: 10px 15px;
      background: red;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      font-size: 16px;
    }
    .buy-btn:hover {
      background: darkred;
    }
    #loginBox, #registerBox {
      margin-top: 100px;
    }
    input {
      padding: 10px;
      margin: 5px;
      border-radius: 5px;
      border: none;
    }
    button {
      padding: 10px 20px;
      margin-top: 10px;
      background: red;
      color: #fff;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    #topupBox {
      display:none;
      background:#222;
      padding:15px;
      border-radius:10px;
      margin-top:15px;
    }
  </style>
</head>
<body>
  <header>เฮียนุโซน</header>

  <!-- กล่องล็อกอิน -->
  <div id="loginBox">
    <h2>🔑 เข้าสู่ระบบ</h2>
    <input type="text" id="username" placeholder="ชื่อผู้ใช้"><br>
    <input type="password" id="password" placeholder="รหัสผ่าน"><br>
    <button onclick="login()">เข้าสู่ระบบ</button>
    <br>
    <button onclick="showRegister()">สมัครสมาชิก</button>
  </div>

  <!-- กล่องสมัครสมาชิก -->
  <div id="registerBox" style="display:none;">
    <h2>📝 สมัครสมาชิก</h2>
    <input type="text" id="regUser" placeholder="ชื่อผู้ใช้"><br>
    <input type="password" id="regPass" placeholder="รหัสผ่าน"><br>
    <button onclick="register()">สมัคร</button>
    <br>
    <button onclick="backToLogin()">กลับไปเข้าสู่ระบบ</button>
  </div>

  <!-- หน้าหลัก -->
  <div id="mainPage" style="display:none;">
    <div class="container">
      <h2>📦 สินค้าทั้งหมด</h2>
      <p>ผู้ใช้ปัจจุบัน: <span id="currentUser"></span></p>
      <p>เครดิตของคุณ: <span id="credits">0</span></p>

      <div class="product">
        <h3>🐔 ไก่ตัน (ของเด็ด - มีโอกาสเกลือ)</h3>
        <p>ราคา: 2 เครดิต</p>
        <button class="buy-btn" onclick="buy('ไก่ตัน (ของเด็ด)', 2)">ซื้อเลย</button>
      </div>

      <div class="product">
        <h3>💎 เซิร์ฟเวอร์ VIP (1 เดือน)</h3>
        <p>ราคา: 50 เครดิต</p>
        <button class="buy-btn" onclick="buy('VIP 1 เดือน', 50)">ซื้อเลย</button>
      </div>

      <div class="product">
        <h3>🔥 เซิร์ฟเวอร์ VIP (ถาวร)</h3>
        <p>ราคา: 999 เครดิต</p>
        <button class="buy-btn" onclick="buy('VIP ถาวร', 999)">ซื้อเลย</button>
      </div>

      <button class="buy-btn" onclick="showTopup()">💳 เติมเครดิต</button>
    </div>
  </div>

  <!-- กล่องเติมเครดิต -->
  <div id="topupBox">
    <h3>💳 เติมเครดิตผ่าน TrueMoney</h3>
    <p>โอนเงินไปที่เบอร์:</p>
    <h2 style="color:orange;">080-221-5880</h2>
    <p>ใส่จำนวนที่โอน (เครดิต):</p>
    <input type="number" id="topupAmount" placeholder="จำนวนเครดิต" min="1">
    <p>📎 อัปโหลดสลิป:</p>
    <input type="file" id="slipUpload" accept="image/*">
    <p id="countdown" style="color:red; font-weight:bold; margin-top:10px;">เหลือเวลา: 1:30</p>
    <button onclick="submitTopup()">ส่งสลิป</button>
    <button onclick="cancelTopup()">ยกเลิก</button>
  </div>

  <footer>© 2025 เฮียนุโซน - ร้านไก่ตัน Blox Fruits</footer>

  <script>
    // โหลด users จาก localStorage
    let users = JSON.parse(localStorage.getItem("users")) || {};

    // ถ้ายังไม่มี admin ให้สร้างใหม่
    if (!users["admin"]) {
      users["admin"] = { password: "112233", credits: 999999 };
      saveUsers();
    }

    let currentUser = null;
    let topupRequests = [];
    let countdownTimer;
    let timeLeft = 90;
    let topupActive = false;

    function saveUsers() {
      localStorage.setItem("users", JSON.stringify(users));
    }

    function showRegister() {
      document.getElementById("loginBox").style.display = "none";
      document.getElementById("registerBox").style.display = "block";
    }

    function backToLogin() {
      document.getElementById("registerBox").style.display = "none";
      document.getElementById("loginBox").style.display = "block";
    }

    function register() {
      let user = document.getElementById("regUser").value;
      let pass = document.getElementById("regPass").value;
      if (!user || !pass) {
        alert("❌ กรุณากรอกข้อมูลให้ครบ");
        return;
      }
      if (users[user]) {
        alert("❌ มีชื่อผู้ใช้นี้อยู่แล้ว");
        return;
      }
      users[user] = { password: pass, credits: 0 };
      saveUsers();
      alert("✅ สมัครสมาชิกสำเร็จ! กรุณาเข้าสู่ระบบ");
      backToLogin();
    }

    function login() {
      const user = document.getElementById("username").value;
      const pass = document.getElementById("password").value;
      if (users[user] && users[user].password === pass) {
        currentUser = user;
        document.getElementById("loginBox").style.display = "none";
        document.getElementById("mainPage").style.display = "block";
        document.getElementById("currentUser").innerText = user;
        document.getElementById("credits").innerText = users[user].credits;

        if (user === "admin") {
          let adminDiv = document.createElement("div");
          adminDiv.id = "adminPanel";
          adminDiv.innerHTML = `
            <h2>🛠 เมนูแอดมิน - ตรวจสอบการเติมเครดิต</h2>
            <div id="requestsList"></div>
          `;
          document.body.insertBefore(adminDiv, document.querySelector("footer"));
          renderRequests();
        }
      } else {
        alert("❌ ชื่อผู้ใช้หรือรหัสผ่านไม่ถูกต้อง");
      }
    }

    function buy(item, price) {
      if (users[currentUser].credits >= price) {
        users[currentUser].credits -= price;
        saveUsers();
        document.getElementById("credits").innerText = users[currentUser].credits;

        if (item.includes("ของเด็ด")) {
          let rand = Math.random() * 100;
          let result = (rand < 89) ? "🧂 เกลือ!" : "🎁 รางวัลใหญ่!!";
          alert("✅ คุณซื้อ " + item + " ราคา " + price + " เครดิต\nผลลัพธ์: " + result);
        } else {
          alert("✅ คุณซื้อ " + item + " ราคา " + price + " เครดิต สำเร็จ!");
        }

        let container = document.querySelector(".container");
        let btn = document.createElement("button");
        btn.innerText = "📥 รับสินค้า";
        btn.className = "buy-btn";
        btn.onclick = () => {
          window.location.href = "https://www.facebook.com/anuwat.wawisai.2025?mibextid=ZbWKwL";
        };
        container.appendChild(btn);

      } else {
        alert("❌ เครดิตไม่พอสำหรับซื้อ " + item);
      }
    }

    function showTopup() {
      document.getElementById("topupBox").style.display = "block";
      startCountdown();
    }

    function startCountdown() {
      timeLeft = 90;
      topupActive = true;
      updateCountdown();
      countdownTimer = setInterval(() => {
        timeLeft--;
        updateCountdown();
        if (timeLeft <= 0) {
          clearInterval(countdownTimer);
          alert("⏰ หมดเวลา กรุณาเริ่มใหม่");
          document.getElementById("topupBox").style.display = "none";
          topupActive = false;
        }
      }, 1000);
    }

    function updateCountdown() {
      let minutes = Math.floor(timeLeft / 60);
      let seconds = timeLeft % 60;
      document.getElementById("countdown").innerText =
        "เหลือเวลา: " + minutes + ":" + (seconds < 10 ? "0"+seconds : seconds);
    }

    function cancelTopup() {
      clearInterval(countdownTimer);
      document.getElementById("topupBox").style.display = "none";
      topupActive = false;
    }

    function submitTopup() {
      if (!topupActive) {
        alert("❌ ไม่มีการทำรายการเติมเงินอยู่");
        return;
      }
      let amount = document.getElementById("topupAmount").value;
      let slip = document.getElementById("slipUpload").files[0];
      if (!amount || amount <= 0) {
        alert("❌ กรุณากรอกจำนวนเครดิต");
        return;
      }
      if (!slip) {
        alert("❌ กรุณาอัปโหลดสลิปการโอนเงิน");
        return;
      }
      topupRequests.push({ user: currentUser, amount: parseInt(amount), slip: slip.name });
      alert("📤 ส่งข้อมูลการเติมเครดิตสำเร็จ! กรุณารอแอดมินตรวจสอบ");
      clearInterval(countdownTimer);
      document.getElementById("topupBox").style.display = "none";
      topupActive = false;
      if (currentUser === "admin") renderRequests();
    }

    function renderRequests() {
      let listDiv = document.getElementById("requestsList");
      listDiv.innerHTML = "";
      topupRequests.forEach((req, index) => {
        let div = document.createElement("div");
        div.style.background = "#333";
        div.style.margin = "10px";
        div.style.padding = "10px";
        div.style.borderRadius = "5px";
        div.innerHTML = `
          <p>👤 ผู้ใช้: ${req.user}</p>
          <p>💰 เครดิตที่ขอ: ${req.amount}</p>
          <p>📎 ไฟล์สลิป: ${req.slip}</p>
          <button onclick="approveTopup(${index})">✅ ยืนยัน</button>
          <button onclick="rejectTopup(${index})">❌ ปฏิเสธ</button>
        `;
        listDiv.appendChild(div);
      });
    }

    function approveTopup(index) {
      let req = topupRequests[index];
      users[req.user].credits += req.amount;
      saveUsers();
      alert("✅ ยืนยันการเติมเครดิตให้ " + req.user + " จำนวน " + req.amount);
      topupRequests.splice(index, 1);
      renderRequests();
      if (currentUser === req.user) {
        document.getElementById("credits").innerText = users[currentUser].credits;
      }
    }

    function rejectTopup(index) {
      let req = topupRequests[index];
      alert("❌ ปฏิเสธการเติมเครดิตของ " + req.user);
      topupRequests.splice(index, 1);
      renderRequests();
    }
  </script>
</body>
</html>
