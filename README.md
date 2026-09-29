# dayumi

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Streak</title>
<style>
:root{--bg:#0d0f0e;--panel:#141715;--line:#2b302d;--ink:#e4e7e1;--mute:#8b918c;--g:#3fae6f;--dg:#1f6b45;--r:#a83a3a;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
html{height:100%;scroll-padding-top:env(safe-area-inset-top,0px);background:var(--bg)}
*{box-sizing:border-box}
[hidden]{display:none!important}
body{margin:0;height:100%;background:var(--bg);color:var(--ink);font-family:"SF Mono",Menlo,Consolas,"Courier New",monospace;display:flex}
button{font:inherit;color:inherit;cursor:pointer}
#rail{width:72px;flex:none;border-right:1px solid var(--line);padding:10px 8px;display:flex;flex-direction:column;gap:8px}
#rail button{background:transparent;border:1px solid transparent;border-radius:10px;padding:10px 0;display:flex;flex-direction:column;align-items:center;gap:4px;font-size:20px;color:var(--mute)}
#rail.min{width:36px;padding:10px 4px}
#rail.min button[data-v]{display:none}
#rail button span{font-size:10px}
#rail button.on{background:var(--panel);border-color:var(--line);color:var(--ink)}
#main{flex:1;min-width:0;overflow-y:auto;padding:14px}
h1{font-size:18px;margin:0 0 12px;font-weight:600}
.grid{display:grid;gap:10px}
.blk{background:var(--panel);border:1px solid var(--line);border-radius:10px;padding:16px}
.two{grid-template-columns:1fr 1fr}
.bar{height:10px;margin-bottom:8px}
.bar i{display:block;height:100%;background:var(--dg);width:0;border-radius:2px}
.cyc{font-size:12px;color:var(--mute);margin-bottom:22px}
.cnt{font-size:30px;letter-spacing:-1px;white-space:nowrap}
.lbl{font-size:11px;color:var(--mute);margin-top:4px}
.rk{margin-top:16px;font-size:13px}
.act{font-size:15px;text-align:center;padding:16px}
.urge{border-color:var(--ink);background:transparent}
.rel{border-color:var(--r);color:#e08a8a;background:transparent}
.mh{display:flex;justify-content:space-between;align-items:center;margin-bottom:10px}
.mh button{background:transparent;border:1px solid var(--line);border-radius:8px;padding:6px 12px}
.cal{display:grid;grid-template-columns:repeat(7,1fr);gap:4px}
.wd{font-size:11px;color:var(--mute);text-align:center;padding-bottom:2px}
.c{aspect-ratio:1;display:flex;align-items:center;justify-content:center;border:1px solid var(--line);border-radius:6px;font-size:13px}
.c.g{background:var(--g);border-color:var(--g);color:#06140c}
.c.dg{background:var(--dg);border-color:var(--dg)}
.c.red{background:var(--r);border-color:var(--r)}
.c.t{outline:2px solid var(--ink);outline-offset:1px}
.lg{margin-top:14px;font-size:12px;color:var(--mute);display:grid;gap:6px}
.lg i{display:inline-block;width:12px;height:12px;border-radius:3px;margin-right:8px;vertical-align:-1px}
.stat b{display:block;font-size:22px;margin-top:4px;font-weight:600}
.stat{font-size:12px;color:var(--mute)}
.rb{border:1px solid var(--line);border-radius:8px;padding:12px;display:flex;justify-content:space-between;color:var(--mute)}
.rb.on{background:var(--dg);border-color:var(--dg);color:var(--ink)}
.sec{font-size:12px;color:var(--mute);margin:8px 0 0}
input{font:inherit;width:100%;padding:10px;border:1px solid var(--line);border-radius:8px;background:transparent;color:var(--ink);color-scheme:dark;margin:8px 0}
.btn{width:100%;background:transparent;border:1px solid var(--ink);border-radius:8px;padding:10px}
#ov{position:fixed;inset:0;background:rgba(0,0,0,.75);display:flex;align-items:flex-end;justify-content:center;padding:16px;padding-bottom:calc(16px + env(safe-area-inset-bottom,0px))}
.sheet{width:100%;max-width:480px;background:var(--panel);border:1px solid var(--line);border-radius:12px;padding:18px;display:grid;gap:10px}
.sheet h2{margin:0;font-size:18px}
.m{margin:0;color:var(--mute);font-size:13px}
.task{display:flex;align-items:center;gap:12px;text-align:left;background:transparent;border:1px solid var(--ink);border-radius:8px;padding:12px}
.task b{width:20px;height:20px;border:1px solid var(--ink);border-radius:4px;display:flex;align-items:center;justify-content:center;font-size:14px}
.task.on{background:var(--dg);border-color:var(--dg)}
.task.on b{border-color:var(--ink)}
:disabled{opacity:.35;cursor:default}
:focus-visible{outline:2px solid var(--g);outline-offset:2px}
</style>
</head>
<body>
<nav id="rail">
<button id="tg" aria-label="Collapse menu"><i>‹</i></button>
<button data-v="timer"><i>◷</i><span>Timer</span></button>
<button data-v="stats"><i>▦</i><span>Stats</span></button>
<button data-v="profile"><i>◉</i><span>Profile</span></button>
</nav>
<main id="main"></main>
<div id="ov" hidden></div>
<script>
var $=function(i){return document.getElementById(i)};
var KEY="streak-v1",S=null,DAY=864e5;
try{S=JSON.parse(localStorage.getItem(KEY))}catch(e){}
if(!S)S={start:null,first:null,rel:[],best:0}
function save(){try{localStorage.setItem(KEY,JSON.stringify(S))}catch(e){}}
var view="timer",vm=new Date();vm.setDate(1);
var R=[[1,"noob"],[3,"novice"],[7,"committed"],[14,"man of words"],[29,"a MAN"]];
var TASKS=["Stand up","Take deep breath","Drink water","Walk","Do push-ups (optional)"],done=[];
function p2(n){return String(n).padStart(2,"0")}
function rankIdx(d){var r=-1;for(var i=0;i<R.length;i++)if(d>=R[i][0])r=i;return r}
function loc(t){var d=new Date(t-new Date(t).getTimezoneOffset()*6e4);return d.toISOString().slice(0,16)}

function tick(){
var el=$("cnt");if(!el)return;
var ms=S.start?Date.now()-S.start:0,d=Math.floor(ms/DAY),h=Math.floor(ms%DAY/36e5),m=Math.floor(ms%36e5/6e4),sc=Math.floor(ms%6e4/1e3);
el.textContent=p2(d)+" : "+p2(h)+" : "+p2(m)+" : "+p2(sc);
$("bar").style.width=(ms%DAY/DAY*100)+"%";
$("cyc").textContent=S.start?h+"h "+m+"m of 24h":"not started";
var ri=rankIdx(d);$("rk").textContent="rank: "+(ri<0?"none yet":R[ri][1]);
}
function startTimer(){var n=Date.now();S.start=n;S.first=n;S.rel=[];save();render()}

function dayClass(y,m,d){
var s=new Date(y,m,d).getTime(),e=new Date(y,m,d+1).getTime(),i,r=-1;
for(i=0;i<S.rel.length;i++)if(S.rel[i]>=s&&S.rel[i]<e)r=i;
if(r>=0){var nx=S.rel[r+1]||Date.now();return nx-S.rel[r]>=12*36e5?"dg":"red"}
if(S.first&&e<=Date.now()&&s>=S.first)return"g";
return"";
}

function timerView(){
return '<h1>Timer</h1><div class="grid"><div class="blk"><div class="bar"><i id="bar"></i></div><div class="cyc" id="cyc"></div><div class="cnt" id="cnt"></div><div class="lbl">days : hours : minutes : seconds</div><div class="rk" id="rk"></div></div>'+
'<button class="blk act" style="border-color:var(--g);color:var(--g)" onclick="startTimer()"'+(S.start?' disabled':'')+'>'+(S.start?'Timer running':'Start timer')+'</button><div class="grid two"><button class="blk act urge" onclick="openUrge()">Urge</button><button class="blk act rel" onclick="openRel()"'+(S.start?'':' disabled')+'>Relapse</button></div></div>';
}

function statsView(){
var y=vm.getFullYear(),m=vm.getMonth(),f=new Date(y,m,1).getDay(),dim=new Date(y,m+1,0).getDate(),t=new Date(),h="",i;
var mn=vm.toLocaleString("en",{month:"long"});
h+='<h1>Stats</h1><div class="blk"><div class="mh"><button onclick="mv(-1)" aria-label="Previous month">‹</button><span>'+mn+' '+y+'</span><button onclick="mv(1)" aria-label="Next month">›</button></div><div class="cal">';
"SMTWTFS".split("").forEach(function(w){h+='<div class="wd">'+w+'</div>'});
for(i=0;i<f;i++)h+="<div></div>";
for(i=1;i<=dim;i++){
var td=t.getFullYear()===y&&t.getMonth()===m&&t.getDate()===i;
h+='<div class="c '+dayClass(y,m,i)+(td?" t":"")+'">'+i+"</div>";
}
h+='</div><div class="lg"><div><i style="background:var(--g)"></i>full day clean</div><div><i style="background:var(--dg)"></i>relapse day, then 12h+ clean</div><div><i style="background:var(--r)"></i>relapse day</div></div></div>';
return h;
}

function profileView(){
var ms=S.start?Date.now()-S.start:0,d=Math.floor(ms/DAY),best=Math.max(S.best,ms),ri=rankIdx(d),h="";
h+='<h1>Profile</h1><div class="grid"><div class="blk stat">Rank<b>'+(ri<0?"none yet":R[ri][1])+'</b></div>';
h+='<div class="grid two"><div class="blk stat">Best streak<b>'+Math.floor(best/DAY)+' days</b></div><div class="blk stat">Relapses<b>'+S.rel.length+'</b></div></div>';
R.forEach(function(r,i){h+='<div class="rb'+(d>=r[0]?" on":"")+'"><span>'+r[1]+'</span><span>'+r[0]+'d</span></div>'});
h+='<div class="blk"><div class="sec">Streak start</div><input type="datetime-local" id="st" value="'+loc(S.start||Date.now())+'"><button class="btn" onclick="setStart()">Set start</button><p class="m" style="margin-top:8px">Setting the start begins the timer and clears relapse history.</p></div></div>';
return h;
}

function render(){
document.querySelectorAll("#rail button[data-v]").forEach(function(b){b.className=b.dataset.v===view?"on":""});
$("main").innerHTML=view==="timer"?timerView():view==="stats"?statsView():profileView();
tick();
}
document.querySelectorAll("#rail button[data-v]").forEach(function(b){b.onclick=function(){view=b.dataset.v;render()}});
function mv(k){vm.setMonth(vm.getMonth()+k);render()}
function setStart(){
var t=new Date($("st").value).getTime();
if(!t||t>Date.now())return;
S.start=t;S.first=t;S.rel=[];save();view="timer";render();
}

function show(h){$("ov").innerHTML='<div class="sheet">'+h+"</div>";$("ov").hidden=false}
function closeOv(){$("ov").hidden=true}
function openUrge(){done=[];drawUrge()}
function drawUrge(){
var h='<h2>Cool down</h2><p class="m">Tick each one as you do it.</p>';
TASKS.forEach(function(t,i){h+='<button class="task'+(done[i]?" on":"")+'" onclick="tog('+i+')"><b>'+(done[i]?"✓":"")+"</b>"+t+"</button>"});
h+='<button class="btn" onclick="closeOv()">Close</button>';
show(h);
}
function tog(i){done[i]=!done[i];drawUrge()}
function openRel(){
show('<h2>Log a relapse?</h2><p class="m">Your streak resets to zero. Your best streak is kept.</p><button class="btn" style="border-color:var(--r);color:#e08a8a" onclick="doRel()">Confirm relapse</button><button class="btn" onclick="closeOv()">Cancel</button>');
}
function doRel(){
var now=Date.now();
S.best=Math.max(S.best,now-S.start);
S.rel.push(now);S.start=now;save();closeOv();render();
}
function rail(min){
$("rail").className=min?"min":"";
$("tg").firstChild.textContent=min?"☰":"‹";
$("tg").setAttribute("aria-label",min?"Open menu":"Collapse menu");
try{localStorage.setItem("streak-rail",min?"1":"0")}catch(e){}
}
$("tg").onclick=function(){rail($("rail").className!=="min")};
var rm=false;try{rm=localStorage.getItem("streak-rail")==="1"}catch(e){}
rail(rm);
save();render();setInterval(tick,1000);
</script>
</body>
</html>
