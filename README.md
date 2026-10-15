<!DOCTYPE html>
<html lang="id"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Kalkulator</title>
<style>

:root{color-scheme:dark;--bg:#030303;--fg:#e6dccb;--blood:#a3141f;box-sizing:border-box}
html,body{margin:0;height:100%;background:var(--bg);color:var(--fg);font-family:Georgia,"Times New Roman",serif;overflow:hidden;touch-action:none}
canvas{display:block;width:100%;height:100%}
#hud{position:fixed;top:0;left:0;right:0;padding:calc(env(safe-area-inset-top,0px) + 10px) 16px 0;display:flex;justify-content:space-between;font-size:17px;pointer-events:none;text-shadow:0 0 6px #000}
#msg{position:fixed;left:0;right:0;bottom:24%;text-align:center;opacity:0;transition:opacity .6s;pointer-events:none;font-style:italic;padding:0 20px}
#ov{position:fixed;inset:0;background:rgba(0,0,0,.9);display:flex;flex-direction:column;align-items:center;justify-content:center;text-align:center;padding:24px;gap:16px}
#ov h1{font-size:46px;letter-spacing:5px;color:var(--blood);margin:0;font-weight:normal}
#ov p{max-width:320px;margin:0;line-height:1.6;opacity:.85}
button{background:var(--blood);color:#fff;border:0;padding:13px 34px;font-size:18px;font-family:inherit;border-radius:3px}
#flash{position:fixed;inset:0;background:#c00;opacity:0;pointer-events:none}
.hidden{display:none!important}
#obj{position:fixed;top:calc(env(safe-area-inset-top,0px) + 40px);left:0;right:0;text-align:center;font-size:14px;opacity:.75;text-shadow:0 0 6px #000;pointer-events:none}

#calc{--c-bg:#101214;--c-key:#22262b;--c-fn:#343a41;--c-op:#1d3b36;--c-opt:#5fe0c3;--c-eq:#2fb297;--c-eqt:#06211c;--c-tx:#eef0f2;--c-mut:#8a939c;--c-ln:#2c3137;position:fixed;inset:0;background:var(--c-bg);color:var(--c-tx);font-family:system-ui,-apple-system,"Segoe UI",sans-serif;padding:calc(env(safe-area-inset-top,0px) + 10px) 16px calc(env(safe-area-inset-bottom,0px) + 16px)}
@media (prefers-color-scheme:light){#calc{--c-bg:#eceae4;--c-key:#fbfaf7;--c-fn:#dcd9d0;--c-op:#cfe6df;--c-opt:#0f5a4a;--c-eq:#17725f;--c-eqt:#fff;--c-tx:#1b1d1f;--c-mut:#6b7078;--c-ln:#d6d3ca}}
#calc .in{position:relative;width:100%;max-width:400px;height:100%;margin:0 auto;display:flex;flex-direction:column}
#calc button{border:0;padding:0;background:none;color:inherit;font-family:inherit;cursor:pointer;touch-action:manipulation;-webkit-tap-highlight-color:transparent}
#calc button:focus-visible{outline:2px solid var(--c-eq);outline-offset:2px}
#calc .bar{display:flex;justify-content:flex-start;min-height:40px}
#calc .bar button{font-size:15px;color:var(--c-mut);padding:8px 4px}
#calc .disp{flex:1;min-height:0;display:flex;flex-direction:column;justify-content:flex-end;text-align:right;padding:0 4px 14px;font-variant-numeric:tabular-nums}
#cx{color:var(--c-mut);font-size:22px;min-height:28px;overflow:hidden;white-space:nowrap}
#cd{font-size:68px;font-weight:300;line-height:1.15;overflow:hidden;white-space:nowrap}
#calc .pad{display:grid;grid-template-columns:repeat(4,1fr);gap:10px}
#calc .pad button{height:clamp(54px,10.4vh,82px);border-radius:22px;font-size:27px;background:var(--c-key);transition:transform .06s,filter .06s}
#calc .pad button:active{transform:scale(.94);filter:brightness(1.25)}
#calc .pad .fn{background:var(--c-fn);font-size:24px}
#calc .pad .op{background:var(--c-op);color:var(--c-opt);font-size:32px}
#calc .pad .eq{background:var(--c-eq);color:var(--c-eqt);font-size:32px}
#hp{position:absolute;inset:0;background:var(--c-bg);display:flex;flex-direction:column}
#hp .hh{display:flex;justify-content:space-between;align-items:center;padding:8px 4px 12px;font-size:18px}
#hp .hh button{font-size:15px;color:var(--c-mut);padding:8px 6px}
#hl{flex:1;overflow-y:auto;touch-action:pan-y}
#hl p{color:var(--c-mut);text-align:center;margin-top:40px}
#calc #hl button{width:100%;display:flex;flex-direction:column;align-items:flex-end;gap:2px;padding:12px 4px;border-bottom:1px solid var(--c-ln);font-size:26px;font-variant-numeric:tabular-nums}
#hl button span{font-size:15px;color:var(--c-mut)}
</style></head><body>
<div id="calc"><div class="in"><div class="bar"><button id="hb">Riwayat</button></div>
<div class="disp"><div id="cx"></div><div id="cd">0</div></div>
<div class="pad" id="pad"></div>
<div id="hp" class="hidden"><div class="hh"><strong>Riwayat</strong><span><button id="hx">Hapus</button><button id="hc">Tutup</button></span></div><div id="hl"></div></div>
</div></div>
<div id="game" class="hidden">
<div id="hud"><span id="k">Kunci 0/3</span><span id="s"></span></div><div id="obj"></div>
<div id="msg"></div><div id="flash"></div>
<div id="ov"><h1 id="t">RUMAH TUA</h1>
<p id="d">Mobilmu mogok di tengah hujan, dan satu-satunya tempat berteduh adalah Rumah Tua. Begitu kamu masuk, pintunya terkunci sendiri.<br><br><b>Tujuan:</b> temukan 3 kunci emas di dalam rumah, lalu kabur lewat pintu hijau. Jangan sampai Penjaga Rumah menangkapmu.</p>
<p id="h" style="font-size:14px;opacity:.6">Layar sentuh: geser kiri untuk jalan, geser kanan untuk melihat.<br>Komputer: WASD + mouse.</p>
<button id="b">Masuk</button></div>
</div>
<script type="text/plain" id="gsrc">
const N=15,C=4,H=3,$=id=>document.getElementById(id),clamp=(v,a=-1,b=1)=>Math.max(a,Math.min(b,v));
// ---- maze ----
const g=[];for(let i=0;i<N;i++)g.push(Array(N).fill(1));
(function(){const st=[[1,1]];g[1][1]=0;while(st.length){const[x,y]=st[st.length-1];
const d=[[2,0],[-2,0],[0,2],[0,-2]].filter(([a,b])=>{const nx=x+a,ny=y+b;return nx>0&&ny>0&&nx<N-1&&ny<N-1&&g[ny][nx]});
if(!d.length){st.pop();continue}const[a,b]=d[Math.random()*d.length|0];g[y+b/2][x+a/2]=0;g[y+b][x+a]=0;st.push([x+a,y+b])}
for(let i=0;i<16;i++){const x=1+(Math.random()*(N-2)|0),y=1+(Math.random()*(N-2)|0);if((x+y)%2)g[y][x]=0}})();
const dirs=[[1,0],[-1,0],[0,1],[0,-1]];
function bfs(sx,sy){const d=g.map(r=>r.map(()=>-1));d[sy][sx]=0;const q=[[sx,sy]];
for(let i=0;i<q.length;i++){const[x,y]=q[i];for(const[a,b]of dirs){const nx=x+a,ny=y+b;if(g[ny]&&g[ny][nx]===0&&d[ny][nx]<0){d[ny][nx]=d[y][x]+1;q.push([nx,ny])}}}return d}
const d0=bfs(1,1),open=[];for(let y=1;y<N-1;y+=2)for(let x=1;x<N-1;x+=2)open.push([x,y,d0[y][x]]);
open.sort((a,b)=>b[2]-a[2]);const ex=open[0],far=open.slice(1,13).sort(()=>Math.random()-.5);
// ---- scene ----
const R=new THREE.WebGLRenderer({antialias:true});R.setSize(innerWidth,innerHeight);R.setPixelRatio(Math.min(devicePixelRatio,2));document.body.prepend(R.domElement);
const S=new THREE.Scene();S.background=new THREE.Color(0x020202);S.fog=new THREE.FogExp2(0x020202,.17);
const cam=new THREE.PerspectiveCamera(70,innerWidth/innerHeight,.1,60);cam.rotation.order='YXZ';S.add(cam);
function tex(col,n,rep){const c=document.createElement('canvas');c.width=c.height=128;const x=c.getContext('2d');x.fillStyle=col;x.fillRect(0,0,128,128);
for(let i=0;i<n;i++){x.fillStyle=`rgba(0,0,0,${Math.random()*.4})`;x.fillRect(Math.random()*128,Math.random()*128,Math.random()*30+2,Math.random()*3+1)}
const t=new THREE.CanvasTexture(c);t.wrapS=t.wrapT=THREE.RepeatWrapping;t.repeat.set(rep,rep);return t}
const wm=new THREE.MeshStandardMaterial({map:tex('#5a4a3c',260,1),roughness:1}),wg=new THREE.BoxGeometry(C,H,C);
for(let y=0;y<N;y++)for(let x=0;x<N;x++)if(g[y][x]){const m=new THREE.Mesh(wg,wm);m.position.set(x*C,H/2,y*C);S.add(m)}
const mid=(N-1)*C/2,fl=new THREE.Mesh(new THREE.PlaneGeometry(N*C,N*C),new THREE.MeshStandardMaterial({map:tex('#2b2119',500,8),roughness:1}));
fl.rotation.x=-Math.PI/2;fl.position.set(mid,0,mid);S.add(fl);
const ce=new THREE.Mesh(new THREE.PlaneGeometry(N*C,N*C),new THREE.MeshStandardMaterial({color:0x151210}));ce.rotation.x=Math.PI/2;ce.position.set(mid,H,mid);S.add(ce);
S.add(new THREE.AmbientLight(0x1a1a2a,.45));
const sp=new THREE.SpotLight(0xffe8c8,1.7,24,.5,.55,1.2);cam.add(sp);sp.position.set(0,0,0);sp.target.position.set(0,0,-1);cam.add(sp.target);
const pl2=new THREE.PointLight(0x553322,.6,6);cam.add(pl2);
// ---- objects ----
const door=new THREE.Mesh(new THREE.BoxGeometry(1.8,2.4,.2),new THREE.MeshBasicMaterial({color:0x22aa55}));door.position.set(ex[0]*C,1.2,ex[1]*C);S.add(door);
const keys=far.slice(0,3).map(([x,y])=>{const m=new THREE.Mesh(new THREE.OctahedronGeometry(.28),new THREE.MeshBasicMaterial({color:0xffcc33}));m.position.set(x*C,1.1,y*C);S.add(m);return m});
const gh=new THREE.Group(),gm=new THREE.MeshBasicMaterial({color:0xc8d0e0,transparent:true,opacity:.6});
const bd=new THREE.Mesh(new THREE.ConeGeometry(.55,1.9,10),gm);bd.position.y=-.1;gh.add(bd);
const hd=new THREE.Mesh(new THREE.SphereGeometry(.3,12,12),gm);hd.position.y=1.0;gh.add(hd);
for(const s of[-1,1]){const e=new THREE.Mesh(new THREE.SphereGeometry(.06),new THREE.MeshBasicMaterial({color:0x000000}));e.position.set(s*.12,1.05,.27);gh.add(e)}
const gl=new THREE.PointLight(0xff2233,.8,7);gh.add(gl);gh.position.set(far[3][0]*C,1.2,far[3][1]*C);S.add(gh);
// ---- state ----
const pl={x:C,z:C},st={mode:'menu',keys:0,t:0,grace:10};let yaw=-Math.PI/2*0,pitch=0;yaw=Math.PI;
// start cell (1,1): open directions are +x or +z; face the open one
if(g[1][2]===0)yaw=-Math.PI/2;
const K={},mv={x:0,y:0};addEventListener('keydown',e=>K[e.code]=1);addEventListener('keyup',e=>K[e.code]=0);
addEventListener('mousemove',e=>{if(document.pointerLockElement){yaw-=e.movementX*.0022;pitch=clamp(pitch-e.movementY*.0022,-1.2,1.2)}});
const T={};
addEventListener('touchstart',e=>{for(const t of e.changedTouches)T[t.identifier]={x:t.clientX,y:t.clientY,sx:t.clientX,sy:t.clientY,m:t.clientX<innerWidth/2}});
addEventListener('touchmove',e=>{e.preventDefault();for(const t of e.changedTouches){const o=T[t.identifier];if(!o)continue;
if(o.m){mv.x=clamp((t.clientX-o.sx)/45);mv.y=clamp((t.clientY-o.sy)/45)}else{yaw-=(t.clientX-o.x)*.006;pitch=clamp(pitch-(t.clientY-o.y)*.006,-1.2,1.2);o.x=t.clientX;o.y=t.clientY}}},{passive:false});
addEventListener('touchend',e=>{for(const t of e.changedTouches){if(T[t.identifier]&&T[t.identifier].m){mv.x=mv.y=0}delete T[t.identifier]}});
addEventListener('resize',()=>{R.setSize(innerWidth,innerHeight);cam.aspect=innerWidth/innerHeight;cam.updateProjectionMatrix()});
// ---- audio ----
let AC;function audio(){try{AC=new(window.AudioContext||window.webkitAudioContext)();const gn=AC.createGain();gn.gain.value=.05;gn.connect(AC.destination);
[48,51].forEach((f,i)=>{const o=AC.createOscillator();o.type=i?'sine':'sawtooth';o.frequency.value=f;o.connect(gn);o.start()})}catch(e){}}
function thump(){if(!AC)return;const o=AC.createOscillator(),g2=AC.createGain();o.frequency.setValueAtTime(70,AC.currentTime);o.frequency.exponentialRampToValueAtTime(30,AC.currentTime+.2);
g2.gain.setValueAtTime(.4,AC.currentTime);g2.gain.exponentialRampToValueAtTime(.001,AC.currentTime+.25);o.connect(g2);g2.connect(AC.destination);o.start();o.stop(AC.currentTime+.3)}
function scream(){if(!AC)return;const n=AC.sampleRate*1.3,b=AC.createBuffer(1,n,AC.sampleRate),d=b.getChannelData(0);for(let i=0;i<n;i++)d[i]=(Math.random()*2-1)*(1-i/n);
const s=AC.createBufferSource(),g2=AC.createGain();s.buffer=b;g2.gain.value=.7;s.connect(g2);g2.connect(AC.destination);s.start();
const o=AC.createOscillator();o.type='sawtooth';o.frequency.setValueAtTime(900,AC.currentTime);o.frequency.exponentialRampToValueAtTime(200,AC.currentTime+1);o.connect(g2);o.start();o.stop(AC.currentTime+1.2)}
// ---- helpers ----
const solid=(x,z)=>{const gx=Math.round(x/C),gy=Math.round(z/C);return !g[gy]||g[gy][gx]!==0};
const blocked=(x,z,r=.35)=>solid(x+r,z)||solid(x-r,z)||solid(x,z+r)||solid(x,z-r);
let mt;function msg(t){const m=$('msg');m.textContent=t;m.style.opacity=1;clearTimeout(mt);mt=setTimeout(()=>m.style.opacity=0,2800)}
let pd,pcx=1,pcy=1,pdT=0;
function end(win){st.mode=win?'win':'dead';document.exitPointerLock&&document.exitPointerLock();
if(!win){scream();const f=$('flash');f.style.transition='none';f.style.opacity=.9;setTimeout(()=>{f.style.transition='opacity 1.5s';f.style.opacity=0},80)}
const sec=Math.round(st.t);$('t').textContent=!win?'ENDING: PENJAGA RUMAH':st.t<150?'ENDING: PELARIAN SEMPURNA':'ENDING: SELAMAT, TAPI...';
$('d').textContent=!win?`Ia menemukanmu setelah ${sec} detik, dengan ${st.keys} dari 3 kunci. Rumah Tua kini punya penghuni baru.`:st.t<150?`Kamu keluar dalam ${sec} detik, secepat bayangan. Hujan sudah reda. Saat menoleh, rumah itu gelap dan tenang, seolah tak pernah ada yang masuk.`:`Kamu berhasil keluar setelah ${sec} detik. Saat mesin mobil menyala, ada sosok putih di jendela lantai dua, masih menatapmu. Ia belum selesai denganmu.`;
$('h').classList.add('hidden');$('b').textContent='Main lagi';$('b').onclick=()=>location.reload();setTimeout(()=>$('ov').classList.remove('hidden'),win?0:900)}
$('b').onclick=()=>{$('ov').classList.add('hidden');st.mode='play';audio();if(!('ontouchstart'in window))try{R.domElement.requestPointerLock()}catch(e){};msg('Temukan 3 kunci emas, lalu kabur lewat pintu hijau.')};
R.domElement.addEventListener('click',()=>{if(st.mode==='play'&&!('ontouchstart'in window))try{R.domElement.requestPointerLock()}catch(e){}});
// ---- loop ----
const clock=new THREE.Clock();let hbT=0;
(function loop(){requestAnimationFrame(loop);const dt=Math.min(clock.getDelta(),.05),t=clock.elapsedTime;
keys.forEach(k=>{k.rotation.y=t*2;k.position.y=1.1+Math.sin(t*2+k.position.x)*.1});
if(st.mode==='play'){st.t+=dt;st.grace-=dt;
let f=(K.KeyW||K.ArrowUp?1:0)-(K.KeyS||K.ArrowDown?1:0)-mv.y,r=(K.KeyD||K.ArrowRight?1:0)-(K.KeyA||K.ArrowLeft?1:0)+mv.x;
const l=Math.hypot(f,r);if(l>1){f/=l;r/=l}
const sx=(-Math.sin(yaw)*f+Math.cos(yaw)*r)*3.2*dt,sz=(-Math.cos(yaw)*f-Math.sin(yaw)*r)*3.2*dt;
if(!blocked(pl.x+sx,pl.z))pl.x+=sx;if(!blocked(pl.x,pl.z+sz))pl.z+=sz;
cam.position.set(pl.x,1.6+(l>.1?Math.sin(t*9)*.04:0),pl.z);cam.rotation.set(pitch,yaw,0);
// keys & door
keys.forEach(k=>{if(k.visible&&Math.hypot(k.position.x-pl.x,k.position.z-pl.z)<1.3){k.visible=false;st.keys++;$('k').textContent=`Kunci ${st.keys}/3`;msg(['Catatan di lantai: "Ia penjaga rumah ini. Jangan biarkan ia mendekat."','Catatan: "Setiap kunci yang kamu ambil membuatnya makin cepat."','Semua kunci terkumpul. Kabur lewat pintu hijau!'][st.keys-1])}});
const dd=Math.hypot(door.position.x-pl.x,door.position.z-pl.z);
$('obj').textContent=st.keys<3?`Tujuan: temukan ${3-st.keys} kunci emas lagi`:`Tujuan: kabur lewat pintu hijau (${Math.round(dd)} m)`;
if(dd<1.6){if(st.keys>=3)end(true);else msg(`Pintu terkunci. Butuh ${3-st.keys} kunci lagi.`)}
// ghost
pcx=Math.round(pl.x/C);pcy=Math.round(pl.z/C);pdT-=dt;if(pdT<=0||!pd){pd=bfs(pcx,pcy);pdT=.3}
if(st.grace<=0){const gx=Math.round(gh.position.x/C),gy=Math.round(gh.position.z/C);let tx=pl.x,tz=pl.z;
if(!(gx===pcx&&gy===pcy)){let best=1e9;for(const[a,b]of dirs){const nx=gx+a,ny=gy+b;const v=pd[ny]&&pd[ny][nx];if(v>=0&&v<best){best=v;tx=nx*C;tz=ny*C}}}
const dx=tx-gh.position.x,dz=tz-gh.position.z,m=Math.hypot(dx,dz)||1,spd=1.5+st.keys*.35;gh.position.x+=dx/m*spd*dt;gh.position.z+=dz/m*spd*dt}
gh.position.y=1.2+Math.sin(t*2)*.12;gh.rotation.y=Math.atan2(pl.x-gh.position.x,pl.z-gh.position.z);
const gd=Math.hypot(gh.position.x-pl.x,gh.position.z-pl.z);gm.opacity=.35+Math.random()*.3;
if(gd<1)end(false);
// atmosphere
sp.intensity=gd<7&&Math.random()<.25?.15:(Math.random()<.01?.3:1.7);
hbT-=dt;if(gd<14&&hbT<=0){thump();hbT=clamp(gd/12,.35,1.2)}
$('s').textContent=gd<5?'Dekat sekali...':gd<9?'Ada yang mengikutimu':''}
R.render(S,cam)})();
</script>
<script>
const $c=id=>document.getElementById(id);
const K=[['AC','fn'],['⌫','fn'],['%','fn'],['÷','op'],['7'],['8'],['9'],['×','op'],['4'],['5'],['6'],['−','op'],['1'],['2'],['3'],['+','op'],['±','fn'],['0'],['.'],['=','eq']];
const pad=$c('pad');K.forEach(([l,c])=>{const b=document.createElement('button');b.textContent=l;if(c)b.className=c;b.onclick=()=>press(l);pad.appendChild(b)});
let ex='',just=false,live=true,hist=[];
const isOp=ch=>'÷×−+'.includes(ch);
function fmt(s){return s.replace(/\d+\.?\d*/g,m=>{const[i,d]=m.split('.');const a=i.replace(/\B(?=(\d{3})+(?!\d))/g,'.');return m.includes('.')?a+','+(d||''):a})}
function calc(e){let s=e.replace(/[÷×−+]$/,'');if(!s)return null;
s=s.replace(/(\d+\.?\d*)([+−])(\d+\.?\d*)%/g,'$1$2($1*$3/100)').replace(/%/g,'/100').replace(/×/g,'*').replace(/÷/g,'/').replace(/−/g,'-').replace(/--/g,'+');
if(!/^[0-9+\-*/.() ]+$/.test(s))return null;s=s.replace(/(^|[^0-9.])0+(?=\d)/g,'$1');
try{const r=Function('"use strict";return ('+s+')')();if(!isFinite(r))return NaN;return Math.abs(r)>=1e15?+r.toExponential(6):+r.toFixed(10)}catch(x){return null}}
function show(){const d=$c('cd');d.textContent=fmt(ex)||'0';const n=d.textContent.length;d.style.fontSize=n>14?'30px':n>10?'40px':n>7?'52px':'68px';
if(!just){const r=/[÷×−+%]/.test(ex.slice(1))?calc(ex):null;$c('cx').textContent=r===null||Number.isNaN(r)?'':'= '+fmt(String(r))}}
function press(v){if(!live)return;if(navigator.vibrate)navigator.vibrate(6);
if(ex==='Error')ex='';
if(v==='AC'){ex='';just=false}
else if(v==='⌫'){if(just){ex='';just=false}else ex=ex.slice(0,-1)}
else if(v==='±'){just=false;ex=ex.replace(/(^|[÷×−+])(−?)(\d*\.?\d+)$/,(m,p,n,d)=>p+(n?'':'−')+d)}
else if(v==='%'){just=false;if(/\d$/.test(ex))ex+='%'}
else if(v==='='){
if(ex==='6327'&&!just){unlock();return}
const r=calc(ex);if(r===null)return;
if(Number.isNaN(r)){ex='Error';just=false}
else{if(/[÷×−+%]/.test(ex.slice(1))){hist.unshift({e:ex,r:String(r)});if(hist.length>30)hist.pop();$c('cx').textContent=fmt(ex)+' =';ex=String(r);just=true}}}
else if(isOp(v)){just=false;if(ex===''){if(v==='−')ex='−'}else if(ex==='−'){}else if(isOp(ex.slice(-1))){ex=ex.slice(0,-1)+v}else ex+=v}
else{if(just){ex='';just=false}
if(ex.slice(-1)==='%'||ex.length>=18)return;
if(v==='.'){if(/\.\d*$/.test(ex))return;if(ex===''||isOp(ex.slice(-1)))ex+='0'}
if(/^\d$/.test(v))ex=ex.replace(/(^|[÷×−+])0$/,'$1');
ex+=v}
show()}
function renderHist(){const l=$c('hl');l.innerHTML='';
if(!hist.length){const p=document.createElement('p');p.textContent='Belum ada perhitungan.';l.appendChild(p);return}
hist.forEach(h=>{const b=document.createElement('button'),a=document.createElement('span'),r=document.createElement('strong');a.textContent=fmt(h.e)+' =';r.textContent=fmt(h.r);b.appendChild(a);b.appendChild(r);
b.onclick=()=>{ex=h.r;just=false;$c('cx').textContent='';$c('hp').classList.add('hidden');show()};l.appendChild(b)})}
$c('hb').onclick=()=>{renderHist();$c('hp').classList.remove('hidden')};
$c('hc').onclick=()=>$c('hp').classList.add('hidden');
$c('hx').onclick=()=>{hist=[];renderHist()};
addEventListener('keydown',e=>{if(!live)return;const k=e.key,m={'*':'×','/':'÷','-':'−','+':'+','Enter':'=','=':'=','Backspace':'⌫','Escape':'AC','%':'%'};
if(/^[0-9.]$/.test(k))press(k);else if(m[k]){e.preventDefault();press(m[k])}});
function unlock(){live=false;$c('calc').classList.add('hidden');$c('game').classList.remove('hidden');document.title='Rumah Tua';
const s=document.createElement('script');s.src='https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js';
s.onload=()=>{const j=document.createElement('script');j.textContent='(function(){'+$c('gsrc').textContent+'\n})();';document.body.appendChild(j)};document.head.appendChild(s)}
</script></body></html>
