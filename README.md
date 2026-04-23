import { useState, useEffect, useRef, useCallback } from "react";

/* ── PALETTE ─────────────────────────────────────────── */
const C = {
  bg:  "#020207", bg2: "#07070e", bg3: "#0c0c18",
  g:   "#00ff88", c:   "#00d4ff", p:   "#a78bfa",
  o:   "#ff6b35", r:   "#ff3366", t:   "#b8ffb8",
  m:   "#4a8a4a", b:   "rgba(0,255,136,0.13)",
};

/* ── DATA ────────────────────────────────────────────── */
const NAV = [
  { id:"home",   label:"home.sys",    badge:null, icon:"M8 1L1 7v8h5v-5h4v5h5V7z" },
  { id:"proj",   label:"projects.db", badge:"3",  icon:"M2 3h12v2H2zM2 7h12v2H2zM2 11h8v2H2z" },
  { id:"skills", label:"skills.cfg",  badge:null, icon:"M8 1a7 7 0 100 14A7 7 0 008 1zm0 2a5 5 0 110 10A5 5 0 018 3z" },
  { id:"certs",  label:"certs.log",   badge:null, icon:"M8 1l2 5h5l-4 3 1 5-4-3-4 3 1-5-4-3h5z" },
  { id:"edu",    label:"edu.sys",     badge:null, icon:"M8 1L1 5l7 4 7-4M1 9l7 4 7-4" },
];

const PROJECTS = [
  {
    name:"> FOOD_DELIVERY_TIME_PREDICTION",
    date:"FEB–MAR 2025",
    desc:["ML model predicting delivery ETA using restaurant location, prep time, distance, ","traffic & weather",". Tackles Swiggy/Zomato logistics — reducing late deliveries and unrealistic estimates."],
    tags:[["Python","h"],["Scikit-learn","h"],["Regression",""],["Feature Eng.",""],["Geospatial",""]],
    accent: C.g,
  },
  {
    name:"> BANK_FRAUD_DETECTION.sys",
    date:"DEC 2024–FEB 2025",
    desc:["Fraud classification on ","imbalanced banking data"," using Random Forest. Preprocessing, feature engineering, and model comparison (LR, KNN, RF). Evaluated via ","ROC curves"," and feature importance plots."],
    tags:[["Random Forest","h"],["Pandas","h"],["Logistic Reg.",""],["KNN",""],["NumPy",""]],
    accent: C.r,
  },
  {
    name:"> TELECOM_CHURN_ANALYSIS.db",
    date:"OCT–DEC 2024",
    desc:["Deep EDA comparing churned vs retained customers on tenure, contract & monthly charges. Decision Tree + RF with ","SMOTEEN"," for class imbalance. Achieved ","93% precision"," on unseen data."],
    tags:[["SMOTEEN","h"],["Scikit-learn","h"],["Seaborn",""],["Matplotlib",""],["EDA",""]],
    accent: C.c,
  },
];

const SKILLS_GROUPS = [
  { title:"// languages", items:[["Python",90],["SQL",75],["Java",80]] },
  { title:"// ml / data science", items:[["Scikit-learn",85],["Pandas",88],["NumPy",82]] },
  { title:"// bi & tools", items:[["Tableau",70],["PowerBI",68],["MySQL",75],["Excel",72]] },
  { title:"// visualization & dev", items:[["Seaborn",83],["Matplotlib",80],["Git",72],["VS Code",85]] },
];

const CERTS = [
  { abbr:"AI", name:"AI Associate",       org:"Salesforce" },
  { abbr:"ML", name:"AI and ML",          org:"LinkedIn Learning" },
  { abbr:"DA", name:"Data Analytics",     org:"Tata / Forage" },
  { abbr:"DS", name:"Data Science",       org:"Oracle University" },
  { abbr:"DV", name:"Data Visualization", org:"Simplilearn / SkillUP" },
];

const LOGS = [
  ["OK",  "portfolio.boot",  "system initialized successfully"],
  ["INFO","ml_models.db",   "3 projects loaded into memory"],
  ["OK",  "skills.cfg",     "proficiency matrix compiled — 4 modules"],
  ["OK",  "certs.log",      "5 credentials verified and indexed"],
  ["INFO","github.api",     "repository Siva2979 connected"],
  ["WARN","model_train.py", "random forest fitting in progress"],
  ["OK",  "sklearn",        "93% precision on unseen test data"],
  ["INFO","lpu.edu",        "enrollment status: active — cgpa 7.2"],
  ["OK",  "hackerrank",     "gold badge detected — java 350pts"],
  ["INFO","pandas",         "dataframe operations nominal"],
  ["OK",  "leetcode",       "120+ problems solved and indexed"],
  ["INFO","tableau",        "dashboard rendering complete"],
];

/* ── UTILS ───────────────────────────────────────────── */
const pad = n => String(n).padStart(2,"0");
const rnd = (a,b) => a + Math.floor(Math.random()*(b-a));

/* ── GLOBAL STYLES ───────────────────────────────────── */
const GLOBAL_CSS = `
@import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700;900&display=swap');
*{margin:0;padding:0;box-sizing:border-box}
html,body,#root{height:100%;background:#020207}
body{font-family:'Share Tech Mono',monospace;overflow:hidden}
::-webkit-scrollbar{width:4px}
::-webkit-scrollbar-track{background:#07070e}
::-webkit-scrollbar-thumb{background:#1a4a1a;border-radius:2px}

@keyframes pulse{0%,100%{opacity:.2}50%{opacity:1}}
@keyframes scan{0%{top:0}100%{top:100%}}
@keyframes mfall{0%{transform:translateY(-100%);opacity:0}8%{opacity:1}85%{opacity:.12}100%{transform:translateY(110vh);opacity:0}}
@keyframes glitch{0%,85%,100%{clip-path:none;transform:none}86%{clip-path:inset(15% 0 70% 0);transform:translateX(-4px)}87%{clip-path:none}89%{clip-path:inset(65% 0 8% 0);transform:translateX(4px)}90%{clip-path:none}}
@keyframes pfill{from{width:0}to{width:var(--w)}}
@keyframes fadeSlideUp{from{opacity:0;transform:translateY(10px)}to{opacity:1;transform:none}}
@keyframes fadeSlideIn{from{opacity:0;transform:translateX(-8px)}to{opacity:1;transform:none}}
@keyframes borderPulse{0%,100%{border-color:rgba(0,255,136,0.13)}50%{border-color:rgba(0,255,136,0.4)}}
@keyframes countUp{from{opacity:0;transform:translateY(4px)}to{opacity:1;transform:none}}
@keyframes shimmer{0%{background-position:200% 0}100%{background-position:-200% 0}}
@keyframes blink{0%,100%{opacity:1}50%{opacity:0}}

.glitch-name{animation:glitch 8s infinite}
.scan-line{position:fixed;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,rgba(0,255,136,0.06),transparent);animation:scan 6s linear infinite;z-index:9999;pointer-events:none}
.matrix-col{position:absolute;top:0;font-size:12px;color:rgba(0,255,136,0.07);line-height:1.4;animation:mfall linear infinite;pointer-events:none}
.cursor-blink{display:inline-block;animation:blink 1s step-end infinite}

.nav-item{
  display:flex;align-items:center;gap:12px;
  padding:13px 16px;font-size:14px;cursor:pointer;
  border-left:3px solid transparent;
  transition:all .2s ease;user-select:none;
  color:#4a8a4a;position:relative;
}
.nav-item:hover{background:rgba(0,255,136,0.06);color:#00ff88;border-left-color:#00ff88}
.nav-item.active{background:rgba(0,255,136,0.1);color:#00ff88;border-left-color:#00ff88}
.nav-item::after{content:'';position:absolute;right:0;top:0;bottom:0;width:0;background:rgba(0,255,136,0.03);transition:width .2s}
.nav-item:hover::after{width:100%}

.skill-bar-fill{animation:pfill 1.4s cubic-bezier(.4,0,.2,1) both}

.panel{background:#07070e;border:1px solid rgba(0,255,136,0.13);border-radius:4px;animation:borderPulse 6s infinite}
.section-enter{animation:fadeSlideUp .25s ease both}
.log-line{animation:fadeSlideIn .2s ease both}

.stat-card{
  background:#0c0c18;border:1px solid rgba(0,255,136,0.13);border-radius:4px;
  padding:14px 8px;text-align:center;position:relative;overflow:hidden;
  transition:border-color .2s,transform .2s;
}
.stat-card:hover{border-color:rgba(0,255,136,0.35);transform:translateY(-2px)}
.stat-card::before{content:'';position:absolute;top:0;left:0;right:0;height:2px;background:linear-gradient(90deg,transparent,#00ff88,transparent);opacity:.8}

.project-card{
  background:#0c0c18;border:1px solid rgba(0,255,136,0.1);border-radius:4px;
  padding:16px;border-left:3px solid transparent;
  transition:all .2s ease;flex:1;display:flex;flex-direction:column;justify-content:space-between;
}
.project-card:hover{background:rgba(0,255,136,0.03)}

.cert-row{
  display:flex;align-items:center;gap:12px;padding:11px 12px;
  background:#0c0c18;border:1px solid rgba(0,255,136,0.13);border-radius:4px;
  transition:border-color .2s;flex:1;
}
.cert-row:hover{border-color:rgba(0,255,136,0.3)}

.edu-card{
  background:#0c0c18;border:1px solid rgba(0,255,136,0.13);border-radius:4px;
  padding:14px 16px;display:flex;flex-direction:column;justify-content:space-between;
  border-left:3px solid;transition:transform .2s;
}
.edu-card:hover{transform:translateX(2px)}

.skill-module{
  background:#0c0c18;border:1px solid rgba(0,255,136,0.13);
  border-radius:4px;padding:14px;display:flex;flex-direction:column;
  transition:border-color .2s;
}
.skill-module:hover{border-color:rgba(0,255,136,0.25)}

.contact-link{
  display:flex;align-items:center;gap:9px;font-size:13px;
  color:#4a8a4a;padding:6px 0;transition:color .15s;
}
.contact-link a{color:#00d4ff;text-decoration:none;transition:color .15s}
.contact-link a:hover{color:#00ff88}
.contact-link:hover{color:#00ff88}
`;

/* ── COMPONENTS ──────────────────────────────────────── */

function MatrixBg() {
  const chars = "アイカキ01クケ10コサシスセタチ";
  const cols = Array.from({length:10},(_,i) => ({
    left: `${i*10+2}%`,
    dur:  `${5+Math.random()*7}s`,
    delay:`${Math.random()*6}s`,
    text: Array.from({length:28},()=>chars[Math.floor(Math.random()*chars.length)]).join("\n"),
  }));
  return (
    <div style={{position:"fixed",inset:0,overflow:"hidden",pointerEvents:"none",zIndex:0}}>
      {cols.map((col,i) => (
        <div key={i} className="matrix-col" style={{
          left:col.left, animationDuration:col.dur, animationDelay:col.delay,
          whiteSpace:"pre", lineHeight:"1.5",
        }}>{col.text}</div>
      ))}
    </div>
  );
}

function TopBar({cpu, time, uptime}) {
  return (
    <div style={{
      position:"relative",zIndex:10,background:C.bg2,
      borderBottom:`1px solid ${C.b}`,padding:"0 20px",
      display:"flex",justifyContent:"space-between",alignItems:"center",
      height:44,flexShrink:0,
    }}>
      <div style={{display:"flex",alignItems:"center",gap:14}}>
        <div style={{display:"flex",gap:8}}>
          {[C.r,"#ffcc00",C.g].map((col,i)=>(
            <div key={i} style={{width:14,height:14,borderRadius:"50%",background:col,
              boxShadow:`0 0 6px ${col}55`}}/>
          ))}
        </div>
        <span style={{fontFamily:"Orbitron,monospace",fontSize:15,color:C.c,letterSpacing:3}}>
          KALYAN_OS v2.0
        </span>
      </div>
      <div style={{display:"flex",alignItems:"center",gap:20,fontSize:13,color:C.m}}>
        <div style={{display:"flex",alignItems:"center",gap:6}}>
          <span style={{width:8,height:8,borderRadius:"50%",background:C.g,
            display:"inline-block",animation:"pulse 1.5s infinite",
            boxShadow:`0 0 6px ${C.g}`}}/>
          <span style={{color:C.g}}>ONLINE</span>
        </div>
        <div style={{color:C.g,fontFamily:"Orbitron,monospace",fontSize:13}}>{time}</div>
        <div>UPTIME:<span style={{color:C.g,marginLeft:6}}>{uptime}</span></div>
        <div>CPU:<span style={{color:C.g,marginLeft:6}}>{cpu}%</span></div>
      </div>
    </div>
  );
}

function Sidebar({active, setActive, cpu, ram, net}) {
  return (
    <div style={{
      width:200,flexShrink:0,background:C.bg2,
      borderRight:`1px solid ${C.b}`,display:"flex",flexDirection:"column",overflow:"hidden",
    }}>
      <div style={{fontSize:11,color:C.m,letterSpacing:2,padding:"12px 16px 6px",textTransform:"uppercase"}}>
        // navigate
      </div>
      {NAV.map(n => (
        <div key={n.id} className={`nav-item${active===n.id?" active":""}`}
          onClick={()=>setActive(n.id)}>
          <svg width={20} height={20} viewBox="0 0 16 16" fill="currentColor" style={{opacity:.75,flexShrink:0}}>
            <path d={n.icon}/>
          </svg>
          <span style={{flex:1}}>{n.label}</span>
          {n.badge && (
            <span style={{fontSize:11,background:"rgba(0,255,136,0.12)",color:C.g,
              padding:"2px 7px",borderRadius:3}}>
              {n.badge}
            </span>
          )}
        </div>
      ))}

      {/* system meters */}
      <div style={{marginTop:"auto",padding:"12px 14px 14px",borderTop:`1px solid ${C.b}`}}>
        {[
          {label:"CPU",val:cpu,color:C.g},
          {label:"RAM",val:ram,color:C.c},
          {label:"NET",val:net,color:C.p},
        ].map(m=>(
          <div key={m.label} style={{marginBottom:8}}>
            <div style={{display:"flex",justifyContent:"space-between",fontSize:11,color:C.m,marginBottom:3}}>
              <span>{m.label}</span><span style={{color:m.color}}>{m.val}%</span>
            </div>
            <div style={{height:3,background:"rgba(0,255,136,0.07)",borderRadius:2,overflow:"hidden"}}>
              <div style={{height:"100%",width:`${m.val}%`,background:m.color,borderRadius:2,
                transition:"width .8s ease",boxShadow:`0 0 6px ${m.color}44`}}/>
            </div>
          </div>
        ))}
        <div style={{display:"flex",justifyContent:"space-between",fontSize:11,color:C.m,marginTop:6}}>
          <span>BUILD</span><span style={{color:C.g}}>2025.04.23</span>
        </div>
      </div>
    </div>
  );
}

/* ── HOME ── */
function HomeSection({logs}) {
  const stats = [
    {id:"s1",target:3,  suffix:"",  label:"ML Projects"},
    {id:"s2",target:120,suffix:"+", label:"LeetCode"},
    {id:"s3",target:93, suffix:"%", label:"Precision"},
    {id:"s4",target:5,  suffix:"",  label:"Certs"},
  ];
  const [counts,setCounts] = useState(stats.map(()=>0));
  useEffect(()=>{
    const timers = stats.map((s,i)=>{
      let v=0; const step=s.target/50;
      return setInterval(()=>{
        v+=step; if(v>=s.target) v=s.target;
        setCounts(c=>{const n=[...c];n[i]=Math.floor(v);return n;});
        if(v>=s.target) clearInterval(timers?.[i]);
      },18);
    });
    return ()=>timers.forEach(clearInterval);
  },[]);

  const contacts = [
    {icon:"M2 4h12v8H2zm0 0l6 5 6-5", href:"mailto:kalyannaidub348@gmail.com", label:"kalyannaidub348@gmail.com"},
    {icon:"M3 2h2l1 4-1.5 1.5a9 9 0 004 4L10 10l4 1v2a1 1 0 01-1 1A13 13 0 012 3a1 1 0 011-1z", href:null, label:"+91 93982 93156"},
    {icon:"M1 1h14v14H1zM5 6v6M5 4v.5M8 6v6M8 6a3 3 0 016 0v6", href:"https://linkedin.com/in/sivakalyan2979", label:"linkedin/sivakalyan2979"},
    {icon:"M8 0C3.6 0 0 3.6 0 8c0 3.5 2.3 6.5 5.5 7.6.4.1.5-.2.5-.4v-1.3C3.7 14.3 3.2 13 3.2 13c-.4-.9-.9-1.1-.9-1.1-.7-.5.1-.5.1-.5.8.1 1.2.8 1.2.8.7 1.2 1.8.8 2.2.6.1-.5.3-.8.5-1C4.7 11.6 3 11 3 8.3c0-.8.3-1.4.8-1.9-.1-.2-.3-.9.1-1.9 0 0 .6-.2 2.1.8a7 7 0 013.8 0c1.4-1 2.1-.8 2.1-.8.4 1 .2 1.7.1 1.9.5.5.7 1.1.7 1.9 0 2.7-1.6 3.3-3.2 3.5.3.2.5.7.5 1.4v2c0 .2.1.5.5.4C13.7 14.5 16 11.5 16 8c0-4.4-3.6-8-8-8z", href:"https://github.com/Siva2979", label:"github/Siva2979"},
  ];

  return (
    <div className="section-enter" style={{flex:1,display:"flex",flexDirection:"column",gap:10,padding:"12px 14px",minHeight:0,overflow:"hidden"}}>
      {/* profile card */}
      <div className="panel" style={{flexShrink:0}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase"}}>
          // profile_node :: active
        </div>
        <div style={{padding:"14px 16px"}}>
          <div style={{display:"grid",gridTemplateColumns:"1.2fr 1fr",gap:16,alignItems:"start"}}>
            <div>
              <div className="glitch-name" style={{
                fontFamily:"Orbitron,monospace",fontSize:32,fontWeight:900,
                color:C.g,letterSpacing:2,lineHeight:1,
                textShadow:`0 0 20px ${C.g}44`,
              }}>KALYAN<span className="cursor-blink" style={{color:C.c}}>_</span></div>
              <div style={{fontSize:13,color:C.c,marginTop:6,letterSpacing:.5}}>
                &gt; DATA_SCIENTIST :: ML_ENGINEER :: CSE_STUDENT
              </div>
              <div style={{fontSize:13,color:C.m,marginTop:10,lineHeight:1.75,maxWidth:380}}>
                Building predictive ML systems that solve real-world problems — fraud detection,
                churn prediction &amp; delivery optimization. B.Tech CSE @ LPU Punjab. Open to opportunities.
              </div>
            </div>
            <div>
              {contacts.map((c,i)=>(
                <div key={i} className="contact-link">
                  <svg width={16} height={16} viewBox="0 0 16 16" fill="currentColor" style={{opacity:.6,flexShrink:0}}>
                    <path d={c.icon}/>
                  </svg>
                  {c.href
                    ? <a href={c.href} target={c.href.startsWith("http")?"_blank":undefined}>{c.label}</a>
                    : <span style={{color:C.t}}>{c.label}</span>}
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>

      {/* stat cards */}
      <div style={{display:"grid",gridTemplateColumns:"repeat(4,1fr)",gap:10,flexShrink:0}}>
        {stats.map((s,i)=>(
          <div key={s.id} className="stat-card">
            <div style={{fontFamily:"Orbitron,monospace",fontSize:26,color:C.g,fontWeight:700,
              textShadow:`0 0 16px ${C.g}66`}}>
              {counts[i]}{s.suffix}
            </div>
            <div style={{fontSize:11,color:C.m,marginTop:4,letterSpacing:.5,textTransform:"uppercase"}}>{s.label}</div>
          </div>
        ))}
      </div>

      {/* live log */}
      <div className="panel" style={{flex:1,minHeight:0,display:"flex",flexDirection:"column"}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase",flexShrink:0}}>
          // system_log :: live_feed
        </div>
        <div style={{flex:1,overflow:"hidden",padding:"10px 14px",display:"flex",flexDirection:"column",gap:2,justifyContent:"flex-end"}}>
          {logs.map((l,i)=>(
            <div key={i} className="log-line" style={{fontSize:12,lineHeight:1.9,color:C.m,animationDelay:`${i*0.05}s`}}>
              <span style={{color:C.p,marginRight:8}}>[{l.ts}]</span>
              <span style={{color:l.type==="OK"?C.g:l.type==="WARN"?C.o:C.c,marginRight:6}}>[{l.type}]</span>
              <span>{l.module}</span>
              <span style={{color:"#2a5a2a"}}> :: </span>
              <span>{l.msg}</span>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

/* ── PROJECTS ── */
function ProjectsSection() {
  return (
    <div className="section-enter" style={{flex:1,display:"flex",flexDirection:"column",padding:"12px 14px",gap:10,minHeight:0}}>
      <div className="panel" style={{flex:1,minHeight:0,display:"flex",flexDirection:"column"}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase",flexShrink:0}}>
          // projects.db :: 3 records loaded
        </div>
        <div style={{flex:1,padding:"12px 14px",display:"flex",flexDirection:"column",gap:10,minHeight:0,overflow:"auto"}}>
          {PROJECTS.map((p,i)=>(
            <div key={i} className="project-card" style={{borderLeftColor:p.accent,
              animationDelay:`${i*0.08}s`}}>
              <div>
                <div style={{display:"flex",justifyContent:"space-between",alignItems:"baseline",marginBottom:8}}>
                  <span style={{fontSize:14,color:p.accent,fontWeight:700,textShadow:`0 0 10px ${p.accent}44`}}>{p.name}</span>
                  <span style={{fontSize:12,color:C.m}}>{p.date}</span>
                </div>
                <div style={{fontSize:13,color:C.m,lineHeight:1.7,marginBottom:10}}>
                  {p.desc.map((d,j)=>j%2===0
                    ? <span key={j}>{d}</span>
                    : <em key={j} style={{color:C.c,fontStyle:"normal"}}>{d}</em>
                  )}
                </div>
              </div>
              <div style={{display:"flex",flexWrap:"wrap",gap:6}}>
                {p.tags.map(([t,cls],j)=>(
                  <span key={j} style={{
                    fontSize:11,padding:"3px 9px",borderRadius:3,
                    border:`1px solid ${cls==="h"?"rgba(0,212,255,0.35)":"rgba(0,255,136,0.25)"}`,
                    color:cls==="h"?C.c:C.g,
                    background:cls==="h"?"rgba(0,212,255,0.05)":"transparent",
                  }}>{t}</span>
                ))}
              </div>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

/* ── SKILLS ── */
function SkillBar({name, pct, delay}) {
  const [w, setW] = useState(0);
  useEffect(()=>{ const t=setTimeout(()=>setW(pct),100+delay); return()=>clearTimeout(t); },[pct,delay]);
  return (
    <div style={{display:"flex",alignItems:"center",gap:10,marginBottom:10}}>
      <span style={{fontSize:13,color:C.m,width:90,flexShrink:0}}>{name}</span>
      <div style={{flex:1,height:5,background:"rgba(0,255,136,0.08)",borderRadius:3,overflow:"hidden"}}>
        <div style={{height:"100%",width:`${w}%`,background:`linear-gradient(90deg,${C.g},${C.c})`,
          borderRadius:3,transition:"width 1.2s cubic-bezier(.4,0,.2,1)",
          boxShadow:`0 0 8px ${C.g}55`}}/>
      </div>
      <span style={{fontSize:12,color:C.g,width:32,textAlign:"right"}}>{pct}%</span>
    </div>
  );
}

function SkillsSection() {
  return (
    <div className="section-enter" style={{flex:1,display:"flex",flexDirection:"column",padding:"12px 14px",gap:10,minHeight:0}}>
      <div className="panel" style={{flex:1,minHeight:0,display:"flex",flexDirection:"column"}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase",flexShrink:0}}>
          // skills.cfg :: proficiency_matrix
        </div>
        <div style={{flex:1,padding:"12px 14px",display:"grid",gridTemplateColumns:"1fr 1fr",gap:10,minHeight:0,overflow:"auto"}}>
          {SKILLS_GROUPS.map((g,gi)=>(
            <div key={gi} className="skill-module">
              <div style={{fontSize:12,color:C.o,letterSpacing:1,textTransform:"uppercase",marginBottom:14,
                borderBottom:`1px solid rgba(255,107,53,0.2)`,paddingBottom:6}}>
                {g.title}
              </div>
              {g.items.map(([name,pct],i)=>(
                <SkillBar key={name} name={name} pct={pct} delay={gi*100+i*80}/>
              ))}
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

/* ── CERTS ── */
function CertsSection() {
  const achievements = [
    {color:C.g, title:"HACKERRANK :: GOLD", sub:"Java — 350+ points"},
    {color:C.c, title:"LEETCODE :: 120+",   sub:"Problems solved in Java"},
  ];
  return (
    <div className="section-enter" style={{flex:1,display:"flex",flexDirection:"column",padding:"12px 14px",gap:10,minHeight:0}}>
      <div className="panel" style={{flex:1,minHeight:0,display:"flex",flexDirection:"column"}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase",flexShrink:0}}>
          // certs.log + achievements.sys + training.db
        </div>
        <div style={{flex:1,padding:"12px 14px",display:"grid",gridTemplateColumns:"1fr 1fr",gap:12,minHeight:0,overflow:"auto"}}>
          {/* certifications */}
          <div style={{display:"flex",flexDirection:"column",gap:8}}>
            <div style={{fontSize:12,color:C.o,letterSpacing:1,textTransform:"uppercase",marginBottom:4}}>// certifications</div>
            {CERTS.map((cert,i)=>(
              <div key={i} className="cert-row" style={{animationDelay:`${i*0.06}s`}}>
                <div style={{
                  width:38,height:38,borderRadius:4,display:"flex",alignItems:"center",justifyContent:"center",
                  fontSize:12,fontWeight:700,background:"rgba(0,255,136,0.06)",color:C.g,
                  border:"1px solid rgba(0,255,136,0.2)",flexShrink:0,
                  boxShadow:`inset 0 0 8px rgba(0,255,136,0.06)`,
                }}>{cert.abbr}</div>
                <div style={{flex:1}}>
                  <div style={{fontSize:13,color:C.t}}>{cert.name}</div>
                  <div style={{fontSize:11,color:C.m,marginTop:2}}>{cert.org}</div>
                </div>
                <span style={{fontSize:10,padding:"2px 8px",background:"rgba(0,255,136,0.06)",color:C.g,
                  border:"1px solid rgba(0,255,136,0.2)",borderRadius:3}}>VERIFIED</span>
              </div>
            ))}
          </div>

          {/* right col */}
          <div style={{display:"flex",flexDirection:"column",gap:10}}>
            <div style={{fontSize:12,color:C.o,letterSpacing:1,textTransform:"uppercase",marginBottom:4}}>// achievements</div>
            {achievements.map((a,i)=>(
              <div key={i} style={{
                background:C.bg3,border:`1px solid rgba(0,255,136,0.13)`,borderRadius:4,
                padding:14,textAlign:"center",display:"flex",flexDirection:"column",
                justifyContent:"center",alignItems:"center",gap:6,flex:1,
              }}>
                <div style={{fontSize:14,fontWeight:700,color:a.color,textShadow:`0 0 12px ${a.color}55`}}>{a.title}</div>
                <div style={{fontSize:12,color:C.m}}>{a.sub}</div>
              </div>
            ))}

            <div style={{fontSize:12,color:C.o,letterSpacing:1,textTransform:"uppercase",marginTop:4}}>// training :: board_infinity</div>
            <div style={{background:C.bg3,border:`1px solid rgba(0,255,136,0.13)`,borderRadius:4,padding:14,flex:1}}>
              <div style={{fontSize:12,color:C.o,letterSpacing:1,textTransform:"uppercase",marginBottom:10}}>
                Data Science Trainee — Jun–Jul 2024
              </div>
              {[
                ["Covered","Python, EDA, ML, Data Visualization"],
                ["Tools","SQL data extraction, Tableau dashboards"],
                ["Focus","Statistical analysis, predictive modeling"],
                ["Output","Actionable insights from large datasets"],
              ].map(([k,v])=>(
                <div key={k} style={{fontSize:13,color:C.m,lineHeight:1.9}}>
                  {k}: <em style={{color:C.c,fontStyle:"normal"}}>{v}</em>
                </div>
              ))}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

/* ── EDU ── */
function EduSection() {
  const schools = [
    {span:2, color:C.g, name:"Lovely Professional University — Jalandhar, Punjab",
     deg:"Bachelor of Technology :: Computer Science & Engineering · CGPA 7.2 / 10.0 · Full-time enrollment",
     yr:"AUG 2022 — PRESENT", tag:"ACTIVE"},
    {span:1, color:C.c, name:"Sri Chaitanya Junior College",
     deg:"Class 12 — Intermediate · Score 81%\nGuntur, Andhra Pradesh",
     yr:"JUN 2020 — APR 2022", tag:null},
    {span:1, color:C.o, name:"ST. Ann's School — Ponnur",
     deg:"Class 10 — ICSE Board\nGuntur, Andhra Pradesh",
     yr:"UNTIL APR 2022", tag:null},
  ];
  const softSkills = ["Adaptability","Problem-Solving","Time Management","Convincing Skills","Team Collaboration","Analytical Thinking"];

  return (
    <div className="section-enter" style={{flex:1,display:"flex",flexDirection:"column",padding:"12px 14px",gap:10,minHeight:0}}>
      <div className="panel" style={{flex:1,minHeight:0,display:"flex",flexDirection:"column"}}>
        <div style={{padding:"8px 14px",borderBottom:`1px solid ${C.b}`,fontSize:11,color:C.c,letterSpacing:2,textTransform:"uppercase",flexShrink:0}}>
          // edu.sys :: academic_history
        </div>
        <div style={{flex:1,padding:"12px 14px",display:"grid",gridTemplateColumns:"1fr 1fr",
          gridTemplateRows:"auto auto auto",gap:10,minHeight:0,overflow:"auto"}}>
          {schools.map((s,i)=>(
            <div key={i} className="edu-card" style={{borderLeftColor:s.color,
              gridColumn:s.span===2?"span 2":"auto",
              animationDelay:`${i*0.08}s`,
            }}>
              <div>
                <div style={{fontSize:14,color:s.color,fontWeight:700,marginBottom:5,
                  textShadow:`0 0 10px ${s.color}44`}}>{s.name}</div>
                <div style={{fontSize:13,color:C.m,lineHeight:1.6,whiteSpace:"pre-line"}}>{s.deg}</div>
              </div>
              <div style={{display:"flex",alignItems:"center",gap:10,marginTop:8,fontSize:12,color:C.c}}>
                {s.yr}
                {s.tag && <span style={{fontSize:10,padding:"2px 8px",background:"rgba(0,255,136,0.08)",
                  color:C.g,border:"1px solid rgba(0,255,136,0.25)",borderRadius:3}}>{s.tag}</span>}
              </div>
            </div>
          ))}
          {/* soft skills */}
          <div className="edu-card" style={{borderLeftColor:C.p,gridColumn:"span 2"}}>
            <div style={{fontSize:13,color:C.p,fontWeight:700,marginBottom:10}}>// soft_skills.cfg</div>
            <div style={{display:"flex",flexWrap:"wrap",gap:8}}>
              {softSkills.map(s=>(
                <span key={s} style={{fontSize:12,padding:"4px 12px",border:"1px solid rgba(167,139,250,0.3)",
                  color:C.p,borderRadius:3,background:"rgba(167,139,250,0.04)",
                  transition:"background .15s",cursor:"default",
                }}>{s}</span>
              ))}
            </div>
          </div>
        </div>
      </div>
    </div>
  );
}

/* ── FOOTER ── */
function Footer() {
  return (
    <div style={{
      position:"relative",zIndex:10,background:C.bg2,
      borderTop:`1px solid ${C.b}`,padding:"0 20px",
      display:"flex",justifyContent:"space-between",alignItems:"center",
      fontSize:12,color:C.m,height:30,flexShrink:0,
    }}>
      <div>KALYAN_OS v2.0 :: <span style={{color:C.g}}>ALL SYSTEMS OPERATIONAL</span></div>
      <div>STACK: <span style={{color:C.g}}>Python · ML · SQL · Java · Tableau · PowerBI</span></div>
    </div>
  );
}

/* ── ROOT APP ─────────────────────────────────────────── */
export default function App() {
  const [active, setActive]   = useState("home");
  const [time, setTime]       = useState("--:--:--");
  const [uptime, setUptime]   = useState("00:00");
  const [cpu, setCpu]         = useState(72);
  const [ram, setRam]         = useState(58);
  const [net, setNet]         = useState(84);
  const [logs, setLogs]       = useState([]);
  const t0 = useRef(Date.now());
  const li = useRef(0);

  // clock
  useEffect(()=>{
    const tick=()=>{
      const n=new Date();
      setTime(`${pad(n.getHours())}:${pad(n.getMinutes())}:${pad(n.getSeconds())}`);
      const s=Math.floor((Date.now()-t0.current)/1000);
      setUptime(`${pad(Math.floor(s/60))}:${pad(s%60)}`);
    };
    tick();
    const id=setInterval(tick,1000);
    return()=>clearInterval(id);
  },[]);

  // system meters
  useEffect(()=>{
    const id=setInterval(()=>{
      setCpu(rnd(52,84)); setRam(rnd(44,72)); setNet(rnd(72,95));
    },2500);
    return()=>clearInterval(id);
  },[]);

  // logs
  const addLog = useCallback(()=>{
    const l = LOGS[li.current % LOGS.length];
    const n = new Date();
    setLogs(prev=>[...prev.slice(-11), {
      ts: `${pad(n.getHours())}:${pad(n.getMinutes())}:${pad(n.getSeconds())}`,
      type: l[0], module: l[1], msg: l[2],
      key: Date.now()+Math.random(),
    }]);
    li.current++;
  },[]);

  useEffect(()=>{
    for(let i=0;i<6;i++) setTimeout(addLog, i*160);
    const id=setInterval(addLog,1500);
    return()=>clearInterval(id);
  },[addLog]);

  const sections = {home:<HomeSection logs={logs}/>, proj:<ProjectsSection/>,
    skills:<SkillsSection/>, certs:<CertsSection/>, edu:<EduSection/>};

  return (
    <>
      <style>{GLOBAL_CSS}</style>
      <div className="scan-line"/>
      <MatrixBg/>
      <div style={{
        position:"relative",zIndex:1,height:"100vh",
        display:"flex",flexDirection:"column",background:C.bg,
        color:C.t,fontFamily:"'Share Tech Mono',monospace",overflow:"hidden",
      }}>
        <TopBar cpu={cpu} time={time} uptime={uptime}/>
        <div style={{display:"flex",flex:1,minHeight:0,overflow:"hidden"}}>
          <Sidebar active={active} setActive={setActive} cpu={cpu} ram={ram} net={net}/>
          <div style={{flex:1,display:"flex",flexDirection:"column",minHeight:0,minWidth:0,overflow:"hidden"}}>
            {sections[active]}
          </div>
        </div>
        <Footer/>
      </div>
    </>
  );
}
