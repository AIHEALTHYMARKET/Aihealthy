
<html>
<head>
  <title>赚钱系统 PRO</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
</head>

<body style="font-family:Arial;background:#f0f2f5;">

<div style="background:white;padding:15px;margin:15px;border-radius:10px;">
  <h3>注册 / 登录</h3>
  <input id="email" placeholder="Email"><br><br>
  <input id="password" type="password" placeholder="Password"><br><br>

  <button onclick="register()">注册</button>
  <button onclick="login()">登录</button>
</div>

<div style="background:white;padding:15px;margin:15px;border-radius:10px;">
  <h3>👤 我的推荐链接</h3>
  <p id="myLink">请登录后查看</p>
  <button onclick="copyLink()">复制链接</button>
</div>

<div style="background:white;padding:15px;margin:15px;border-radius:10px;">
  <h3>📚 学习专区</h3>

  <button onclick="checkAccess('newbie')">新人学习</button>
  <button onclick="checkAccess('bronze')">铜章专区</button>
  <button onclick="checkAccess('silver')">银章专区</button>

  <div id="content"></div>
</div>

<!-- 🔥 Firebase 模块化写法 -->
<script type="module">

import { initializeApp } from "https://www.gstatic.com/firebasejs/12.11.0/firebase-app.js";
import { getAuth, createUserWithEmailAndPassword, signInWithEmailAndPassword, onAuthStateChanged } 
from "https://www.gstatic.com/firebasejs/12.11.0/firebase-auth.js";

import { getFirestore, doc, setDoc, getDoc } 
from "https://www.gstatic.com/firebasejs/12.11.0/firebase-firestore.js";

// ✅ 你的 Firebase 配置（已套用）
const firebaseConfig = {
  apiKey: "AIzaSyA_rYrM8nEdLxRTuGM7VqpWUyk2b5CYwM0",
  authDomain: "ai-healthy-market.firebaseapp.com",
  projectId: "ai-healthy-market",
  storageBucket: "ai-healthy-market.firebasestorage.app",
  messagingSenderId: "166202386796",
  appId: "1:166202386796:web:edb71f9ca4b3894d322b49",
  measurementId: "G-67Q53BV551"
};

const app = initializeApp(firebaseConfig);
const auth = getAuth(app);
const db = getFirestore(app);

let currentUserData = null;

// 👉 获取推荐人
function getRef(){
  const urlParams = new URLSearchParams(window.location.search);
  return urlParams.get('ref');
}

// 👉 注册
window.register = async function(){
  let email = document.getElementById("email").value;
  let password = document.getElementById("password").value;

  const res = await createUserWithEmailAndPassword(auth, email, password);
  const user = res.user;

  await setDoc(doc(db, "users", user.uid), {
    email: email,
    level: "newbie",
    ref: getRef() || null,
    createdAt: new Date()
  });

  alert("注册成功");
}

// 👉 登录
window.login = async function(){
  let email = document.getElementById("email").value;
  let password = document.getElementById("password").value;

  await signInWithEmailAndPassword(auth, email, password);
  alert("登录成功");
}

// 👉 监听登录
onAuthStateChanged(auth, async (user)=>{
  if(user){
    const docSnap = await getDoc(doc(db, "users", user.uid));
    currentUserData = docSnap.data();

    let link = window.location.origin + "/?ref=" + user.uid;
    document.getElementById("myLink").innerText = link;
  }
});

// 👉 复制推荐链接
window.copyLink = function(){
  let text = document.getElementById("myLink").innerText;
  navigator.clipboard.writeText(text);
  alert("已复制！");
}

// 👉 权限控制
window.checkAccess = function(type){

  let user = auth.currentUser;
  if(!user){
    alert("请先登录");
    return;
  }

  let level = currentUserData.level;

  if(type === "newbie"){
    showContent("新人课程");
  }
  else if(type === "bronze"){
    if(level === "bronze" || level === "silver"){
      showContent("铜章课程");
    } else {
      alert("请先完成新人课程");
    }
  }
  else if(type === "silver"){
    if(level === "silver"){
      showContent("银章课程");
    } else {
      alert("权限不足");
    }
  }
}

// 👉 内容 + 成交
function showContent(title){
  document.getElementById("content").innerHTML = `
    <h3>${title}</h3>

    <video controls>
      <source src="jimeng-2026-03-23-6814-嘴型不要过度夸张的张开，不要拿掉眼镜，眼睛适当自然的眨眼，眼神要正對鏡頭，眼睛大....mp4">
    </video>

    <br><br>

    <audio controls>
      <source src="audio1.mp3">
    </audio>

    <br><br>

    <button onclick="goWA()">💰 想赚钱？点这里</button>
  `;
}

// 👉 WhatsApp成交
window.goWA = function(){
  window.open("https://wa.me/60123456789?text=我从系统学习后想了解赚钱模式");
}

</script>

</body>
</html>
