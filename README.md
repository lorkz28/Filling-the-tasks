<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>إدارة المهام الذكية</title>

<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#222">

<style>
:root{
  --bg:#f4f4f4;
  --box:#fff;
  --text:#000;
}
.dark{
  --bg:#121212;
  --box:#1e1e1e;
  --text:#fff;
}
body{
  background:var(--bg);
  color:var(--text);
  font-family:Arial;
  padding:20px;
}
.box{
  background:var(--box);
  padding:20px;
  border-radius:10px;
  max-width:500px;
  margin:auto;
}
button,input{
  width:100%;
  padding:10px;
  margin:5px 0;
}
.task{border-bottom:1px solid #555;padding:10px}
.done{color:lime}
.late{color:red}
</style>
</head>

<body>
<div class="box">
<h2>🤖 إدارة المهام الذكية</h2>

<p>⭐ النقاط: <span id="points">100</span></p>
<p>🏆 المستوى: <span id="level">1</span></p>

<button onclick="toggleDark()">🌙 Dark Mode</button>

<input id="taskName" placeholder="اسم المهمة">
<input id="taskTime" type="datetime-local">
<button onclick="addTask()">➕ إضافة</button>
<button onclick="smartSort()">🤖 ترتيب ذكي</button>

<div id="tasks"></div>
</div>

<script>
let tasks=[];
let points=100;
let dark=false;

const tName=taskName;
const tTime=taskTime;
const tDiv=tasks;
const pSpan=points;
const lvl=document.getElementById("level");

// تحميل
if(localStorage.tasks){
 tasks=JSON.parse(localStorage.tasks);
 tasks.forEach(t=>t.time=new Date(t.time));
 points=+localStorage.points;
 dark=localStorage.dark==="1";
 if(dark)document.body.classList.add("dark");
}

function save(){
 localStorage.tasks=JSON.stringify(tasks);
 localStorage.points=points;
 localStorage.dark=dark?"1":"0";
}

function levelCalc(){
 lvl.textContent=Math.floor(points/50)+1;
}

function toggleDark(){
 dark=!dark;
 document.body.classList.toggle("dark");
 save();
}

function addTask(){
 if(!tName.value||!tTime.value)return;
 tasks.push({
  name:tName.value,
  time:new Date(tTime.value),
  done:false,
  punished:false,
  notified:false
 });
 tName.value="";
 tTime.value="";
 save();
 render();
}

function completeTask(i){
 if(!tasks[i].done){
  tasks[i].done=true;
  points+=10;
 }
 save();
 render();
}

function smartSort(){
 tasks.sort((a,b)=>a.time-b.time);
 render();
}

function render(){
 tDiv.innerHTML="";
 const now=new Date();

 tasks.forEach((t,i)=>{
  if(!t.done && now>t.time && !t.punished){
    points-=5;
    t.punished=true;
  }
  if(!t.done && now>=t.time && !t.notified){
    alert("⏰ حان وقت: "+t.name);
    t.notified=true;
  }

  tDiv.innerHTML+=`
   <div class="task ${t.done?"done":t.punished?"late":""}">
    <b>${t.name}</b><br>
    ⏰ ${t.time.toLocaleString()}<br>
    <button onclick="completeTask(${i})">✔ تم</button>
   </div>
  `;
 });

 pSpan.textContent=points;
 levelCalc();
 save();
}

render();
setInterval(render,60000);
</script>
</body>
</html>
# Filling-the-tasks
You must complete your task as quickly as possible so you don't lose points; your level will decrease, and you must persevere.
