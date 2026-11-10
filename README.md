# silver-giggle<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>GAME LIVE</title>
<style>
:root{--bg:#f4f4f7;--card:#fff;--tx:#19151f;--mut:#777;--line:#eee;--soft:#f0edf4;--pu:#9146d5;--pk:#ed3279;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#0f0d14;--card:#1a1722;--tx:#f1ecf7;--mut:#a79db5;--line:#2c2736;--soft:#262131}}
*{box-sizing:border-box}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
body{margin:0;background:var(--bg);color:var(--tx);font-family:Vazirmatn,Tahoma,Arial,sans-serif}
button{cursor:pointer;font-family:inherit}
header{height:60px;background:var(--card);display:flex;align-items:center;justify-content:space-between;padding:0 16px;position:sticky;top:env(safe-area-inset-top,0px);z-index:5}
.logo{font-size:22px;font-weight:900}.logo span{color:#a348e5}
.me{display:flex;gap:8px;align-items:center;font-size:14px}
.me button{border:0;background:var(--soft);color:var(--tx);border-radius:9px;padding:8px 11px}
main{max-width:650px;margin:auto;padding:12px}
.player{height:320px;background:#15121b;border-radius:17px;position:relative;overflow:hidden;display:flex;align-items:center;justify-content:center}
video{width:100%;height:100%;object-fit:cover}
.ph{position:absolute;text-align:center;color:#c9b8dc;pointer-events:none}
.live{position:absolute;top:12px;right:12px;background:#ed2861;color:#fff;padding:7px 12px;border-radius:7px;font-weight:bold;display:none}
.tc{position:absolute;bottom:12px;left:12px;display:flex;gap:8px}
.tc button{background:#0009;color:#fff;border:0;padding:11px;border-radius:10px;min-height:44px}
.sec{background:var(--card);border-radius:16px;padding:15px;margin-top:12px}
.st{font-weight:bold;margin-bottom:12px}
.ctl{display:grid;grid-template-columns:1fr 1fr;gap:10px}
.ctl button{padding:15px;border:0;border-radius:11px;background:var(--soft);color:var(--pu);font-weight:bold;min-height:48px}
.ctl .start{background:var(--pk);color:#fff}.ctl .stop{background:#29232f;color:#fff}
input{width:100%;padding:13px;border:1px solid var(--line);border-radius:10px;background:var(--bg);color:var(--tx);font:inherit;margin-top:8px}
.send{background:var(--pu);color:#fff;border:0;border-radius:10px;padding:13px;margin-top:8px;width:100%;font-size:15px}
.cm{border-bottom:1px solid var(--line);padding:10px 0;font-size:14px;overflow-wrap:anywhere}.cm b{color:#a85fe0}
#msgs{max-height:300px;overflow-y:auto}
.s{font-size:13px;color:var(--mut);margin-top:10px;line-height:1.8}
#auth{position:fixed;inset:0;background:var(--bg);z-index:20;display:flex;align-items:center;justify-content:center;padding:20px;overflow:auto}
.box{background:var(--card);border-radius:20px;padding:22px;width:100%;max-width:380px}
.tabs{display:grid;grid-template-columns:1fr 1fr;gap:6px;margin:16px 0 6px}
.tabs button{border:0;border-radius:10px;padding:11px;background:var(--soft);color:var(--tx)}
.tabs button.on{background:var(--pu);color:#fff}
.err{color:#e0335f;font-size:13px;min-height:20px;margin-top:8px}
</style>
</head>
<body>
<div id="auth">
 <div class="box">
  <div class="logo" style="text-align:center">GAME <span>LIVE</span></div>
  <div class="tabs"><button id="tReg" class="on">ثبت‌نام</button><button id="tLog">ورود</button></div>
  <input id="un" placeholder="نام کاربری" autocomplete="username" maxlength="20">
  <input id="pw" type="password" placeholder="رمز عبور (حداقل ۶ کاراکتر)" autocomplete="current-password">
  <div class="err" id="err"></div>
  <button class="send" id="go">ثبت‌نام</button>
  <div class="s" id="mode">فقط نام و رمز لازم است؛ ایمیل یا شماره نمی‌خواهیم.</div>
 </div>
</div>

<header>
 <div class="logo">GAME <span>LIVE</span></div>
 <div class="me"><span id="who"></span><button id="out">خروج</button></div>
</header>

<main>
 <h3>🔴 پخش زنده</h3>
 <div class="player">
  <video id="cam" autoplay playsinline muted></video>
  <div class="ph" id="ph"><div style="font-size:44px">🎮</div>برای شروع پخش آماده شو</div>
  <div class="live" id="lb">● LIVE</div>
  <div class="tc"><button id="sw">🔄 دوربین</button><button id="mu">🔊 صدا</button></div>
 </div>

 <div class="sec">
  <div class="ctl">
   <button class="start" id="st">🔴 شروع پخش</button>
   <button class="stop" id="sp">⏹ پایان پخش</button>
  </div>
  <div class="s" id="status">وضعیت: پخش شروع نشده است.</div>
 </div>

 <div class="sec" id="chat">
  <div class="st">💬 گفت‌وگوی زنده</div>
  <div id="msgs"></div>
  <input id="m" placeholder="پیام خود را بنویس..." maxlength="300">
  <button class="send" id="snd">ارسال پیام</button>
  <div class="s" id="chatnote"></div>
 </div>
</main>

<script>
const $=id=>document.getElementById(id);
let stream=null,facing='user',live=false,muted=false,me=null,store=null,mode='reg';

/* ---------- helpers ---------- */
const lsGet=k=>{try{return localStorage.getItem(k)}catch(e){return null}};
const lsSet=(k,v)=>{try{localStorage.setItem(k,v)}catch(e){}};
const lsDel=k=>{try{localStorage.removeItem(k)}catch(e){}};
async function sha(s){
 const b=await crypto.subtle.digest('SHA-256',new TextEncoder().encode(s));
 return [...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,'0')).join('');
}
const uid=async n=>(await sha('u:'+n.trim().toLowerCase())).slice(0,40);

/* ---------- storage: shared db if available, else this device only ---------- */
function localStore(){
 const read=()=>{try{return JSON.parse(lsGet('gl_users')||'{}')}catch(e){return {}}};
 return{
  shared:false,
  async getUser(id){return read()[id]||null},
  async setUser(id,o){const u=read();u[id]=o;lsSet('gl_users',JSON.stringify(u))},
  async send(m){const a=JSON.parse(lsGet('gl_chat')||'[]');a.push(m);lsSet('gl_chat',JSON.stringify(a.slice(-50)));this.cb&&this.cb(a)},
  watch(cb){this.cb=cb;try{cb(JSON.parse(lsGet('gl_chat')||'[]'))}catch(e){cb([])}}
 };
}
async function initStore(){
 store=localStore();
 try{
  const c=window.claude&&await claude.use('db');
  if(!c)return;
  store={
   shared:true,
   async getUser(id){const s=await c.collection('users').doc(id).get();return s.exists?s.data():null},
   async setUser(id,o){await c.collection('users').doc(id).set(o)},
   async send(m){await c.collection('chat').doc('m'+Date.now()+Math.random().toString(36).slice(2,7)).set(m)},
   watch(cb){c.collection('chat').orderBy('t','desc').limit(50).onSnapshot(q=>cb(q.docs.map(d=>d.data()).reverse()),()=>{})}
  };
 }catch(e){}
}

/* ---------- auth ---------- */
function setMode(m){
 mode=m;$('tReg').classList.toggle('on',m==='reg');$('tLog').classList.toggle('on',m==='log');
 $('go').textContent=m==='reg'?'ثبت‌نام':'ورود';$('err').textContent='';
}
$('tReg').onclick=()=>setMode('reg');$('tLog').onclick=()=>setMode('log');
$('go').onclick=async()=>{
 const n=$('un').value.trim(),p=$('pw').value;
 if(n.length<3)return $('err').textContent='نام کاربری حداقل ۳ کاراکتر باشد.';
 if(p.length<6)return $('err').textContent='رمز عبور حداقل ۶ کاراکتر باشد.';
 $('go').disabled=true;
 try{
  const id=await uid(n),ex=await store.getUser(id);
  if(mode==='reg'){
   if(ex)return $('err').textContent='این نام قبلاً گرفته شده است.';
   const salt=crypto.randomUUID();
   await store.setUser(id,{name:n,salt,hash:await sha(salt+p)});
  }else{
   if(!ex||ex.hash!==await sha(ex.salt+p))return $('err').textContent='نام کاربری یا رمز اشتباه است.';
  }
  enter(n);
 }catch(e){$('err').textContent='خطا در ارتباط. دوباره تلاش کن.'}
 finally{$('go').disabled=false}
};
function enter(n){
 me=n;lsSet('gl_me',n);$('auth').style.display='none';$('who').textContent='👤 '+n;
 $('chatnote').textContent=store.shared?'چت بین همهٔ کاربران ثبت‌نام‌شده مشترک است.':'این صفحه به پایگاه‌داده مشترک وصل نیست؛ حساب و چت فقط روی همین دستگاه ذخیره می‌شود.';
 store.watch(render);
}
$('out').onclick=()=>{stopLive();lsDel('gl_me');location.reload()};

/* ---------- chat ---------- */
function render(a){
 const box=$('msgs');box.textContent='';
 if(!a.length){const d=document.createElement('div');d.className='cm';d.textContent='هنوز پیامی نیست. اولین نفر باش! 🎮';box.append(d)}
 a.forEach(x=>{const d=document.createElement('div');d.className='cm';
  const b=document.createElement('b');b.textContent=x.name;const t=document.createElement('div');t.textContent=x.text;d.append(b,t);box.append(d)});
 box.scrollTop=box.scrollHeight;
}
async function sendMsg(){
 const v=$('m').value.trim();if(!v||!me)return;$('m').value='';
 try{await store.send({name:me,text:v,t:Date.now()})}catch(e){$('chatnote').textContent='ارسال نشد.'}
}
$('snd').onclick=sendMsg;$('m').onkeydown=e=>{if(e.key==='Enter')sendMsg()};

/* ---------- camera ---------- */
async function startLive(){
 if(live)return;
 try{
  $('status').textContent='در حال اتصال به دوربین...';
  stream=await navigator.mediaDevices.getUserMedia({video:{facingMode:facing,width:{ideal:1280},height:{ideal:720}},audio:true});
  stream.getAudioTracks().forEach(t=>t.enabled=!muted);
  $('cam').srcObject=stream;await $('cam').play();
  live=true;$('ph').style.display='none';$('lb').style.display='block';
  $('status').textContent='🔴 دوربین فعال است (پیش‌نمایش محلی).';
 }catch(e){
  let m='دسترسی به دوربین ممکن نیست.';
  if(e.name==='NotAllowedError')m='اجازهٔ دوربین و میکروفون را بده.';
  if(e.name==='NotFoundError')m='دوربین پیدا نشد.';
  $('status').textContent=m;
 }
}
function stopLive(){
 if(stream){stream.getTracks().forEach(t=>t.stop());stream=null}
 $('cam').srcObject=null;live=false;$('ph').style.display='block';$('lb').style.display='none';
 $('status').textContent='پخش پایان یافت.';
}
async function switchCam(){
 facing=facing==='user'?'environment':'user';
 if(live){stopLive();await startLive()}else $('status').textContent=facing==='user'?'دوربین جلو انتخاب شد.':'دوربین عقب انتخاب شد.';
}
function toggleMute(){
 muted=!muted;if(stream)stream.getAudioTracks().forEach(t=>t.enabled=!muted);
 $('mu').textContent=muted?'🔇 بی‌صدا':'🔊 صدا';
}
$('st').onclick=startLive;$('sp').onclick=stopLive;$('sw').onclick=switchCam;$('mu').onclick=toggleMute;
window.addEventListener('pagehide',()=>{if(stream)stream.getTracks().forEach(t=>t.stop())});

/* ---------- boot ---------- */
initStore().then(()=>{const n=lsGet('gl_me');if(n)enter(n)});
</script>
</body>
</html>
