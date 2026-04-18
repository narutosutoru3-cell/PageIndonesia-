<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Dashboard Hitam Putih</title>

<style>
*{margin:0;padding:0;box-sizing:border-box;font-family:Arial;}

html,body{
    width:100%;
    height:100%;
    overflow:hidden;
    color:white;
}

/* BACKGROUND */
body::before{
    content:"";
    position:fixed;
    width:100%;
    height:100%;
    background:#000;
    z-index:-1;
}

/* BOX LOGIN */
.box{
    background:#111;
    padding:25px;
    border-radius:15px;
    width:90%;
    max-width:350px;
    text-align:center;
    position:absolute;
    top:50%;
    left:50%;
    transform:translate(-50%,-50%);
}

/* INPUT */
input{
    width:100%;
    padding:10px;
    margin:8px 0;
    border:none;
    border-radius:8px;
    background:#222;
    color:white;
}

/* BUTTON */
button{
    width:100%;
    padding:10px;
    margin-top:10px;
    border:none;
    border-radius:8px;
    cursor:pointer;
    font-size:16px;
    font-weight:bold;
    background:white;
    color:black;
}

/* BUTTON KECIL */
#logoutBtn, #profileBar button{
    width:auto;
    padding:5px 10px;
    font-size:12px;
}

/* DASHBOARD */
#dashboard{display:none;height:100%;}

/* Sidebar */
#sidebar{
    width:60px;
    height:100%;
    float:left;
    background:#111;
    border-right:1px solid #333;
    text-align:center;
    padding-top:20px;
}

/* MENU */
.menuItem{
    font-size:22px;
    margin:15px 0;
    cursor:pointer;
    display:flex;
    flex-direction:column;
    align-items:center;
    color:white;
    transition:0.3s;
    border-radius:10px;
    padding:5px 0;
}

.menuItem:hover{
    background:white;
    color:black;
}

/* MAIN */
#mainContent{
    margin-left:60px;
    padding:15px;
    position:relative;
}

/* PROFILE */
#profileBar{
    display:flex;
    align-items:center;
    gap:10px;
}

#profilePic{
    width:50px;
    height:50px;
    border-radius:50%;
    border:2px solid white;
    cursor:pointer;
}

/* LOGOUT */
#logoutBtn{
    position:absolute;
    top:10px;
    right:10px;
}

/* CARD */
.card{
    background:white;
    color:black;
    padding:15px;
    border-radius:10px;
    margin-top:10px;
}

/* ======================= */
/* MODAL */
/* ======================= */
#overlay{
    position:fixed;
    top:0;
    left:0;
    width:100%;
    height:100%;
    background:rgba(0,0,0,0.6);
    display:none; /* 🔥 default disembunyikan */
    justify-content:center;
    align-items:center;
    z-index:100;
}

#popupBox{
    background:#111;
    width:90%;
    max-width:400px;
    border-radius:15px;
    padding:20px;
    position:relative;
    transform:scale(0.8);
    opacity:0;
    animation:fadeIn 0.3s forwards;
}

@keyframes fadeIn{
    to{
        transform:scale(1);
        opacity:1;
    }
}

.closePopup{
    position:absolute;
    top:10px;
    right:15px;
    font-size:20px;
    cursor:pointer;
}

#popupBox h2{margin-bottom:10px;}
#popupBox p{color:#ccc;}
</style>

</head>

<body>

<!-- LOGIN -->
<div class="box" id="loginBox">
<h2>Login</h2>
<input id="loginUser" type="text" placeholder="Username">
<input id="loginPass" type="password" placeholder="Password">
<button onclick="login()">Masuk</button>
<p onclick="showRegister()" style="cursor:pointer;">Belum punya akun? Register</p>
</div>

<!-- REGISTER -->
<div class="box" id="registerBox" style="display:none;">
<h2>Register</h2>
<input id="regUser" type="text" placeholder="Username">
<input id="regPass" type="password" placeholder="Password">
<button onclick="register()">Daftar</button>
<p onclick="showLogin()" style="cursor:pointer;">Sudah punya akun? Login</p>
</div>

<!-- DASHBOARD -->
<div id="dashboard">

<div id="sidebar">
    <div onclick="showNews()" class="menuItem">
        📰
        <span>Berita</span>
    </div>
    <div onclick="showQuiz()" class="menuItem">
        📚
        <span>Soal</span>
    </div>
</div>

<div id="mainContent">

<button id="logoutBtn" onclick="logout()">Logout</button>

<div id="profileBar">
<img id="profilePic">
<input type="file" id="uploadFoto" accept="image/*" style="display:none;">
<div>
<p id="welcome"></p>
<button onclick="hapusFoto()">Hapus Foto</button>
</div>
</div>

<!-- 🔥 MODAL -->
<div id="overlay">
    <div id="popupBox">
        <span class="closePopup" onclick="tutupPopup()">✖</span>
        <h2>Pengumuman</h2>
        <p>Website ini masih dalam tahap pengembangan dan dibuat menggunakan HTML.

Untuk melakukan penarikan (WD) atau deposit (Depo), silakan hubungi admin melalui WhatsApp:
📞 0822-7932-1876

Terima kasih atas pengertiannya 🙏</p>
    </div>
</div>

<div id="newsBox"></div>
<div id="quizBox" style="display:none;"></div>

</div>
</div>

<script>
window.onload = () => {
    loginBox.style.display = "block";
    profilePic.onclick = () => uploadFoto.click();
};

/* SWITCH */
function showRegister(){
    loginBox.style.display="none";
    registerBox.style.display="block";
}
function showLogin(){
    registerBox.style.display="none";
    loginBox.style.display="block";
}

/* REGISTER */
function register(){
let u=regUser.value,p=regPass.value;
if(!u||!p)return alert("Isi semua!");

let users=JSON.parse(localStorage.getItem("users"))||[];
users.push({username:u,password:p,foto:"https://api.dicebear.com/7.x/avataaars/svg?seed="+u});
localStorage.setItem("users",JSON.stringify(users));

alert("Berhasil!");
showLogin();
}

/* LOGIN */
function login(){
let u=loginUser.value,p=loginPass.value;

if(u==="admin"&&p==="123"){masuk(u);return;}

let users=JSON.parse(localStorage.getItem("users"))||[];
let f=users.find(x=>x.username===u&&x.password===p);
f?masuk(u):alert("Salah!");
}

/* MASUK */
function masuk(user){
loginBox.style.display="none";
registerBox.style.display="none";
dashboard.style.display="block";

welcome.innerText="Halo "+user;

let users=JSON.parse(localStorage.getItem("users"))||[];
let d=users.find(x=>x.username===user);

profilePic.src=d.foto;
showNews();

/* 🔥 tampilkan modal */
document.getElementById("overlay").style.display = "flex";
}

/* LOGOUT */
function logout(){
dashboard.style.display="none";
loginBox.style.display="block";

/* 🔥 sembunyikan modal */
document.getElementById("overlay").style.display = "none";
}

/* HAPUS FOTO */
function hapusFoto(){
profilePic.src="https://api.dicebear.com/7.x/avataaars/svg?seed=user";
}

/* TUTUP MODAL */
function tutupPopup(){
    document.getElementById("overlay").style.display="none";
}

/* BERITA */
function showNews(){
quizBox.style.display="none";
newsBox.style.display="block";

let data=[
{judul:"AI 2026",isi:"Semakin canggih"},
{judul:"Cuaca",isi:"Hujan deras"}
];

newsBox.innerHTML="";
data.forEach(d=>{
newsBox.innerHTML += `
<div class="card">
<h4>${d.judul}</h4>
<p>${d.isi}</p>
</div>
`;
});
}

function showQuiz(){
newsBox.style.display="none";
quizBox.style.display="block";
quizBox.innerHTML="<h3>Soal</h3>";
}
</script>

</body>
</html>
