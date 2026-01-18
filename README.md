<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8" />
<title>お金が増える。</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<style>
@import url('https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap');

*{box-sizing:border-box}
body{
  margin:0;
  font-family:"Press Start 2P",monospace;
  background:#5c94fc;
}

#app{
  max-width:720px;
  margin:0 auto;
  padding:16px;
}

#money{
  background:#fff;
  border:4px solid #000;
  padding:12px 16px;
  text-align:center;
  font-size:22px;
  cursor:pointer;
  user-select:none;
}

#money.pop{animation:pop 0.15s}
@keyframes pop{50%{transform:scale(1.2)}}

#clicker{
  display:block;
  margin:16px auto;
  font-size:72px;
  background:none;
  border:none;
  cursor:pointer;
}

#stats{
  text-align:center;
  font-size:12px;
  margin-bottom:16px;
}

.shop details{
  margin-bottom:12px;
  border:4px solid #000;
  background:#f4b400;
}

.shop summary{
  padding:12px;
  cursor:pointer;
  list-style:none;
  background:#f4b400;
}

.shop summary::after{
  content:'▼';
  float:right;
}

.shop details[open] summary::after{
  content:'▲';
}

.shop summary::-webkit-details-marker{display:none}

.shop .content{
  background:#fff;
  padding:12px;
}

.shop button{
  width:100%;
  margin-top:8px;
  padding:10px;
  font-family:inherit;
  border:4px solid #000;
  background:#fff;
  cursor:pointer;
}

.shop button.insufficient{
  background:#ff9b9b;
  color:#600;
}
.shop button.insufficient:hover{
  background:#ff6b6b;
}

.float{
  position:fixed;
  pointer-events:none;
  animation:float 1s forwards;
  font-size:14px;
}
@keyframes float{
  from{opacity:1;transform:translateY(0)}
  to{opacity:0;transform:translateY(-40px)}
}
</style>
</head>
<body>
<div id="app">
  <div id="money">¥0</div>
  <button id="clicker" type="button">💰</button>
  <div id="stats">1回=¥<span id="pc">1</span> / 秒=¥<span id="ps">0</span></div>

  <div class="shop">
    <details open>
      <summary>🖱 クリック強化</summary>
      <div class="content">
        <div>Lv <span id="clv">0</span></div>
        <button id="bClick" type="button">購入</button>
      </div>
    </details>

    <details>
      <summary>🧑‍💻 労働</summary>
      <div class="content">
        <div>所持 <span id="job">0</span></div>
        <button id="bJob" type="button">購入</button>
      </div>
    </details>

    <details>
      <summary>🏢 組織</summary>
      <div class="content">
        <div>所持 <span id="corp">0</span></div>
        <button id="bCorp" type="button">購入</button>
      </div>
    </details>

    <details>
      <summary>📈 投資</summary>
      <div class="content">
        <button id="bInv" type="button">¥500 投資する</button>
      </div>
    </details>
  </div>
</div>

<script>
let money=0;
let perClick=1, perSec=0;
let clickLv=0, job=0, corp=0;

const $=id=>document.getElementById(id);

function yen(n){return '¥'+Math.floor(n).toLocaleString()}

function update(){
  $('money').textContent=yen(money);
  $('pc').textContent=perClick;
  $('ps').textContent=perSec;
  $('clv').textContent=clickLv;
  $('job').textContent=job;
  $('corp').textContent=corp;

  $('bClick').textContent=yen(clickCost());
  $('bJob').textContent=yen(jobCost());
  $('bCorp').textContent=yen(corpCost());

  $('bClick').classList.toggle('insufficient', money<clickCost());
  $('bJob').classList.toggle('insufficient', money<jobCost());
  $('bCorp').classList.toggle('insufficient', money<corpCost());
}

const clickCost=()=>Math.floor(25*Math.pow(1.3,clickLv));
const jobCost=()=>Math.floor(10*Math.pow(1.15,job));
const corpCost=()=>Math.floor(200*Math.pow(1.15,corp));

$('clicker').onclick=e=>{
  money+=perClick;
  pop();
  float('+'+perClick,e.clientX,e.clientY);
  update();
}

$('money').onclick=pop;
function pop(){
  const m=$('money');
  m.classList.remove('pop');
  void m.offsetWidth;
  m.classList.add('pop');
}

$('bClick').onclick=()=>{
  const c=clickCost();
  if(money<c)return;
  money-=c; clickLv++; perClick++; update();
}

$('bJob').onclick=()=>{
  const c=jobCost();
  if(money<c)return;
  money-=c; job++; perSec+=1; update();
}

$('bCorp').onclick=()=>{
  const c=corpCost();
  if(money<c)return;
  money-=c; corp++; perSec+=10; update();
}

$('bInv').onclick=()=>{
  if(money<500)return;
  money-=500;
  if(Math.random()<0.5) money*=2;
  update();
}

function float(t,x,y){
  const d=document.createElement('div');
  d.className='float';
  d.textContent=t;
  d.style.left=x+'px';
  d.style.top=y+'px';
  document.body.appendChild(d);
  setTimeout(()=>d.remove(),1000);
}

setInterval(()=>{money+=perSec; update()},1000);
update();
</script>
</body>
</html>
