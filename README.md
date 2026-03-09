<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>iMLKq | Admin Panel</title>
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-app.js";
  import { getFirestore, collection, addDoc, getDocs, serverTimestamp, query, orderBy } from "https://www.gstatic.com/firebasejs/10.8.0/firebase-firestore.js";

  // إعدادات Firebase الخاصة بك
  const firebaseConfig = {
    apiKey: "AIzaSyB4e0rmoGHEPnNsjpNgKB6osEyJqrzW2IQ",
    authDomain: "imlkq-data.firebaseapp.com",
    projectId: "imlkq-data",
    storageBucket: "imlkq-data.firebasestorage.app",
    messagingSenderId: "1098473505143",
    appId: "1:1098473505143:web:93ed40f13e2596cb2ea172",
    measurementId: "G-BK9QL72FQ4"
  };

  const app = initializeApp(firebaseConfig);
  const db = getFirestore(app);

  // وظيفة تسجيل الزوار
  window.registerUser = async () => {
    const name = document.getElementById('username-input').value;
    if(name.trim() === "") { alert("اكتب اسمك أولاً"); return; }
    
    try {
      await addDoc(collection(db, "visitors"), {
        username: name,
        timestamp: serverTimestamp()
      });
      localStorage.setItem('mlkq_user', name);
      alert("تم تسجيل دخولك بنجاح!");
      location.reload();
    } catch (e) { alert("تأكد من تفعيل Rules في Firebase إلى true"); }
  };

  // وظيفة جلب البيانات لروم الإدارة
  window.loadAdminData = async () => {
    const pass = prompt("أدخل رمز دخول الإدارة:");
    if(pass !== "911admin") { alert("الرمز خطأ!"); return; }
    
    showPage('admin-room');
    const tableBody = document.getElementById('admin-table-body');
    tableBody.innerHTML = "<tr><td colspan='2'>جاري جلب الزوار...</td></tr>";
    
    try {
      const q = query(collection(db, "visitors"), orderBy("timestamp", "desc"));
      const querySnapshot = await getDocs(q);
      tableBody.innerHTML = "";
      querySnapshot.forEach((doc) => {
        const data = doc.data();
        const time = data.timestamp ? data.timestamp.toDate().toLocaleString('ar-EG') : "الآن";
        tableBody.insertAdjacentHTML('beforeend', `
          <tr>
            <td style="padding:15px; border:1px solid #444; color:var(--kick-green);">${data.username}</td>
            <td style="padding:15px; border:1px solid #444; color:#ccc;">${time}</td>
          </tr>
        `);
      });
    } catch (e) { alert("حدث خطأ في جلب البيانات"); }
  };
</script>

<style>
  :root { --main-blue: #0074D9; --kick-green: #2ecc71; --light-blue: #7FDBFF; --gold: #FFD700; }
  body { margin: 0; padding: 0; font-family: 'Segoe UI', sans-serif; background: #000; color: white; background-image: url('IMG_2566.jpeg'); background-size: cover; background-attachment: fixed; }
  body::before { content: ""; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.8); backdrop-filter: blur(8px); z-index: -1; }
  
  .page { display: none; padding: 100px 20px; max-width: 900px; margin: auto; animation: fadeIn 0.5s; }
  .active-page { display: block; }
  @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }

  .openbtn { font-size: 30px; cursor: pointer; background: var(--main-blue); color: white; padding: 10px 15px; border: none; position: fixed; top: 20px; right: 20px; z-index: 4000; border-radius: 10px; }
  .sidebar { height: 100%; width: 0; position: fixed; z-index: 5000; top: 0; right: 0; background: #050505; overflow-x: hidden; transition: 0.4s; padding-top: 60px; border-left: 2px solid var(--main-blue); }
  .sidebar a { padding: 15px 25px; text-decoration: none; font-size: 20px; color: var(--light-blue); display: block; text-align: right; cursor: pointer; }
  .sidebar a:hover { background: rgba(0,116,217,0.2); }

  .admin-box { background: rgba(0,0,0,0.9); padding: 30px; border-radius: 20px; border: 2px solid var(--main-blue); text-align: center; }
  table { width: 100%; border-collapse: collapse; margin-top: 20px; }
  th { background: var(--main-blue); padding: 10px; }
</style>
</head>
<body>

  <button class="openbtn" onclick="openNav()">☰</button>

  <div id="mySidebar" class="sidebar">
    <a onclick="showPage('home-page')">🏠 الرئيسية</a>
    <a onclick="showPage('login-page')">👤 تسجيل المتابعين</a>
    <a onclick="loadAdminData()" style="color:var(--gold);">🔐 روم الإدارة (خاص بك)</a>
  </div>

  <div id="home-page" class="page active-page">
    <div style="text-align: center;">
      <img src="IMG_9806.png" style="width:150px; border-radius:50%; border:3px solid var(--main-blue);">
      <h1 style="font-size: 50px; color: var(--light-blue);">iMLKq</h1>
      <p style="font-size: 20px;">مرحباً بك في لوحة التحكم الخاصة بك</p>
    </div>
  </div>

  <div id="login-page" class="page">
    <div class="admin-box">
      <h2>سجل حضورك يا وحش</h2>
      <input type="text" id="username-input" placeholder="اكتب اسمك هنا" style="width:80%; padding:15px; border-radius:10px; border:1px solid var(--main-blue); background:#111; color:white; text-align:center;">
      <button onclick="registerUser()" style="width:85%; padding:15px; margin-top:20px; background:var(--kick-green); color:black; border:none; border-radius:10px; font-weight:bold; cursor:pointer;">دخول للموقع</button>
    </div>
  </div>

  <div id="admin-room" class="page">
    <div class="admin-box">
      <h2 style="color:var(--gold);"><i class="fas fa-user-shield"></i> بيانات المتابعين الحالية</h2>
      <table>
        <thead>
          <tr>
            <th>الاسم</th>
            <th>وقت الدخول</th>
          </tr>
        </thead>
        <tbody id="admin-table-body">
          </tbody>
      </table>
      <button onclick="location.reload()" style="margin-top:20px; padding:10px 30px; background:transparent; border:1px solid var(--kick-green); color:var(--kick-green); border-radius:10px; cursor:pointer;">تحديث القائمة</button>
    </div>
  </div>

  <script>
    function openNav() { document.getElementById("mySidebar").style.width = "280px"; }
    function closeNav() { document.getElementById("mySidebar").style.width = "0"; }
    function showPage(pageId) {
      document.querySelectorAll('.page').forEach(p => p.classList.remove('active-page'));
      document.getElementById(pageId).classList.add('active-page');
      closeNav();
    }
  </script>
</body>
</html>



الكود ذا احطه سطر جديد
