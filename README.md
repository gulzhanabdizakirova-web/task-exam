<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>SET B (1–30) — Pink Menu (No Popups)</title>
  <style>
    :root{
      --pink:#ff4fa3;
      --pink2:#ff78be;
      --bg:#fff3fa;
      --card:#ffffff;
      --text:#1e1e1e;
      --muted:#6b6b6b;
      --line:#ffd0e6;
    }
    body{
      margin:0; font-family: Arial, sans-serif;
      background: linear-gradient(180deg, var(--bg), #ffffff);
      color:var(--text);
    }
    .wrap{ max-width: 980px; margin: 18px auto; padding: 16px; }
    .top{
      background: linear-gradient(135deg, var(--pink), var(--pink2));
      color:#fff; border-radius:18px; padding:18px;
      box-shadow: 0 10px 25px rgba(255,79,163,.25);
    }
    .top h1{ margin:0 0 6px 0; font-size:22px; }
    .top p{ margin:0; opacity:.95; }
    .panel{
      margin-top:14px;
      display:flex; gap:10px; flex-wrap:wrap; align-items:center;
    }
    select, input{
      padding:12px 12px; border-radius:14px;
      border:1px solid var(--line);
      outline:none; background:#fff;
      min-width:240px;
    }
    button{
      padding:12px 14px; border-radius:14px;
      border:none; cursor:pointer; font-weight:700;
      background:#1e1e1e; color:#fff;
    }
    button:hover{ opacity:.92; }
    .ghost{
      background: transparent; color:#fff; border:1px solid rgba(255,255,255,.7);
    }
    .pinkBtn{
      background: var(--pink);
      color:#fff;
    }
    .card{
      margin-top:16px;
      background:var(--card);
      border:1px solid var(--line);
      border-radius:18px;
      padding:16px;
      box-shadow: 0 6px 18px rgba(0,0,0,.06);
    }
    .title{
      display:flex; justify-content:space-between; gap:12px; flex-wrap:wrap; align-items:center;
    }
    .badge{
      padding:6px 10px; border-radius:999px;
      background: #ffe0f0; border:1px solid var(--line);
      color:#b1005e; font-weight:700; font-size:12px;
    }
    .muted{ color:var(--muted); font-size:13px; margin-top:6px; }
    .row{ display:flex; gap:10px; flex-wrap:wrap; align-items:center; margin-top:12px; }
    .out{
      margin-top:12px;
      padding:12px; border-radius:14px;
      background:#fff0f9;
      border:1px dashed #ff9cd0;
      white-space: pre-wrap;
      min-height: 44px;
    }
    .small{ font-size:12px; color:var(--muted); }
    hr{ border:none; border-top:1px solid var(--line); margin:14px 0; }
    .hide{ display:none; }
  </style>
</head>
<body>
  <div class="wrap">

    <!-- MENU HEADER -->
    <div id="menuHeader" class="top">
      <h1>SET B (1–30) — Pink Menu (NO Popups)</h1>
      <p>Select task number → fill inputs → Run → output.</p>

      <div class="panel">
        <select id="taskSelect">
          <option value="">Choose task (1–30)</option>
        </select>

        <button class="ghost" onclick="openTask()">Open</button>
        <button class="ghost" onclick="resetAll()">Reset</button>
      </div>

      <div class="small" style="margin-top:10px;opacity:.9">
        ✅ Added: Back to Menu / Next / Previous buttons
      </div>
    </div>

    <!-- TASK CARD -->
    <div id="taskCard" class="card hide">
      <div class="title">
        <div>
          <div id="taskHeading" style="font-size:18px;font-weight:800;">Task</div>
          <div id="taskDesc" class="muted"></div>
        </div>
        <div id="taskBadge" class="badge">#</div>
      </div>

      <hr/>

      <!-- navigation buttons -->
      <div class="row">
        <button class="pinkBtn" onclick="backToMenu()">⬅ Back to Menu</button>
        <button onclick="prevTask()">⬅ Previous</button>
        <button onclick="nextTask()">Next ➡</button>
      </div>

      <div id="inputsArea" class="row"></div>

      <div class="row" id="buttonsArea">
        <button onclick="runTask()">Run</button>
      </div>

      <div id="output" class="out">Output will appear here.</div>
    </div>

  </div>

<script>
  const $ = (id) => document.getElementById(id);

  // Fill dropdown 1..30
  for(let i=1;i<=30;i++){
    const opt=document.createElement("option");
    opt.value=i; opt.textContent=Task ${i};
    $("taskSelect").appendChild(opt);
  }

  // Shared state
  let savedArray = [];

  // Timer state for task 29
  let timerId = null;
  let timerT = 0;
  let timerInitial = 0;

  const TASKS = {
    1:{ title:"Reverse words in a sentence",
        desc:"Input a sentence. Output = words reversed.",
        inputs:[{id:"t1", ph:"e.g. Hello World JS"}],
        run:()=> {
          const s=val("t1").trim();
          return s? s.split(/\s+/).reverse().join(" ") : "Enter a sentence.";
        }
    },
    2:{ title:"Extract first and last character from a string",
        desc:"Input a string. Output = first + last char.",
        inputs:[{id:"t2", ph:"e.g. JavaScript"}],
        run:()=> {
          const s=val("t2");
          if(!s) return "Enter a string.";
          return First: ${s[0]} | Last: ${s[s.length-1]};
        }
    },
    3:{ title:"Find longest word from sentence",
        desc:"Input a sentence. Output = longest word.",
        inputs:[{id:"t3", ph:"e.g. I love JavaScript programming"}],
        run:()=> {
          const s=val("t3").trim();
          if(!s) return "Enter a sentence.";
          const words=s.split(/\s+/);
          let longest="";
          for(const w of words) if(w.length>longest.length) longest=w;
          return Longest word: ${longest};
        }
    },
    4:{ title:"Convert string to Title Case",
        desc:"Input: hello world → Output: Hello World",
        inputs:[{id:"t4", ph:"e.g. hello world"}],
        run:()=> val("t4").replace(/\b\w/g, c=>c.toUpperCase())
    },
    5:{ title:"Replace a word in a string",
        desc:"Enter string + find word + replace word.",
        inputs:[
          {id:"t5s", ph:"String (e.g. I love JS)"},
          {id:"t5a", ph:"Find (e.g. JS)"},
          {id:"t5b", ph:"Replace with (e.g. JavaScript)"}
        ],
        run:()=> {
          const s=val("t5s"), a=val("t5a"), b=val("t5b");
          if(!s || !a) return "Enter string + find word.";
          return s.replaceAll(a,b);
        }
    },
    6:{ title:"Count vowels in a string",
        desc:"Counts a,e,i,o,u (case-insensitive).",
        inputs:[{id:"t6", ph:"e.g. education"}],
        run:()=> {
          const s=val("t6");
          const m=s.match(/[aeiou]/gi);
          return Vowels count: ${m?m.length:0};
        }
    },
    7:{ title:"Create an array from user input (NO popup)",
        desc:"Type comma separated values and Save. Saved array is used by tasks 8–10.",
        inputs:[{id:"t7", ph:"Enter values (e.g. a,b,c)"}],
        extraButtons:[{text:"Save Array", action:"saveArray"}],
        run:()=> "Click “Save Array” button to store array."
    },
    8:{ title:"Display third element from array",
        desc:"Uses saved array from task 7.",
        inputs:[],
        run:()=> {
          if(savedArray.length<3) return "Run task 7 and save array first (need at least 3 elements).";
          return 3rd element: ${savedArray[2]};
        }
    },
    9:{ title:"Find last element from array (NO popup)",
        desc:"Uses saved array from task 7 and displays last element.",
        inputs:[],
        run:()=> {
          if(savedArray.length===0) return "Run task 7 and save array first.";
          return Last element: ${savedArray[savedArray.length-1]};
        }
    },
    10:{ title:"Find index of a given element in array",
         desc:"Uses saved array from task 7.",
         inputs:[{id:"t10", ph:"Element to find (e.g. apple)"}],
         run:()=> {
           if(savedArray.length===0) return "Run task 7 and save array first.";
           const el=val("t10").trim();
           return Index of "${el}": ${savedArray.indexOf(el)};
         }
    },
    11:{ title:"Apply map() to square each element",
         desc:"Enter numbers comma separated.",
         inputs:[{id:"t11", ph:"e.g. 1,2,3,4"}],
         run:()=> {
           const arr=toNums(val("t11"));
           return Squared: [${arr.map(n=>n*n).join(", ")}];
         }
    },
    12:{ title:"Sort array in ascending order",
         desc:"Enter numbers comma separated.",
         inputs:[{id:"t12", ph:"e.g. 5,1,9,3"}],
         run:()=> {
           const arr=toNums(val("t12"));
           arr.sort((a,b)=>a-b);
           return Sorted: [${arr.join(", ")}];
         }
    },
    13:{ title:"Use splice to replace elements",
         desc:"Array + index + new value.",
         inputs:[
           {id:"t13a", ph:"Array (e.g. 1,2,3,4)"},
           {id:"t13i", ph:"Index (e.g. 1)"},
           {id:"t13v", ph:"New value (e.g. 99)"}
         ],
         run:()=> {
           const arr=toNums(val("t13a"));
           const i=Number(val("t13i"));
           const v=Number(val("t13v"));
           if(Number.isNaN(i)||Number.isNaN(v)) return "Enter valid index and value.";
           arr.splice(i,1,v);
           return After splice replace: [${arr.join(", ")}];
         }
    },
    14:{ title:"Use splice to delete + insert simultaneously",
         desc:"Array + start index + delete count + insert values.",
         inputs:[
           {id:"t14a", ph:"Array (e.g. 1,2,3,4)"},
           {id:"t14i", ph:"Index (e.g. 2)"},
           {id:"t14d", ph:"Delete count (e.g. 1)"},
           {id:"t14ins", ph:"Insert (e.g. 10,20)"}
         ],
         run:()=> {
           const arr=toNums(val("t14a"));
           const i=Number(val("t14i"));
           const d=Number(val("t14d"));
           const ins=toNums(val("t14ins"));
           if(Number.isNaN(i)||Number.isNaN(d)) return "Enter valid index and delete count.";
           arr.splice(i,d,...ins);
           return After splice delete+insert: [${arr.join(", ")}];
         }
    },
    15:{ title:"Function: arithmetic operations based on user choice",
         desc:"Enter a, b and choose operation.",
         inputs:[
           {id:"t15a", ph:"a (e.g. 10)"},
           {id:"t15b", ph:"b (e.g. 5)"},
           {id:"t15op", type:"select", options:["+", "-", "*", "/"]}
         ],
         run:()=> {
           const a=Number(val("t15a")), b=Number(val("t15b")), op=val("t15op");
           if(Number.isNaN(a)||Number.isNaN(b)) return "Enter valid numbers.";
           if(op==="/" && b===0) return "Cannot divide by 0";
           return Result: ${op==="+"?a+b:op==="-"?a-b:op==="*"?a*b:a/b};
         }
    },
    16:{ title:"Function: calculate factorial",
         desc:"Enter n (>=0).",
         inputs:[{id:"t16", ph:"n (e.g. 5)"}],
         run:()=> {
           const n=Number(val("t16"));
           if(Number.isNaN(n)) return "Enter valid number.";
           if(n<0) return "No factorial for negative numbers.";
           let f=1; for(let i=1;i<=n;i++) f*=i;
           return Factorial(${n}) = ${f};
         }
    },
    17:{ title:"Function: check even/odd",
         desc:"Enter n.",
         inputs:[{id:"t17", ph:"n (e.g. 7)"}],
         run:()=> {
           const n=Number(val("t17"));
           if(Number.isNaN(n)) return "Enter valid number.";
           return (n%2===0) ? "Even" : "Odd";
         }
    },
    18:{ title:"Function: find max of 3 numbers",
         desc:"Enter a, b, c.",
         inputs:[
           {id:"t18a", ph:"a"},
           {id:"t18b", ph:"b"},
           {id:"t18c", ph:"c"}
         ],
         run:()=> {
           const a=Number(val("t18a")), b=Number(val("t18b")), c=Number(val("t18c"));
           if([a,b,c].some(Number.isNaN)) return "Enter all 3 numbers.";
           return Max = ${Math.max(a,b,c)};
         }
    },
    19:{ title:"Function: sum of array values",
         desc:"Enter numbers comma separated.",
         inputs:[{id:"t19", ph:"e.g. 1,2,3,4"}],
         run:()=> {
           const arr=toNums(val("t19"));
           const sum=arr.reduce((x,y)=>x+y,0);
           return Sum = ${sum};
         }
    },
    20:{ title:"Allow only 10 digits input (NO popup)",
         desc:"Enter exactly 10 digits and run.",
         inputs:[{id:"t20", ph:"10 digits (e.g. 1234567890)"}],
         run:()=> {
           const s=val("t20").trim();
           return (/^\\d{10}$/).test(s) ? "Valid ✅ (10 digits)" : "Invalid ❌ (must be exactly 10 digits)";
         }
    },
    21:{ title:"Detect numeric vs string and apply operations (NO popup)",
         desc:"If number → square & double. If string → upper & length.",
         inputs:[{id:"t21", ph:"Enter value (number or text)"}],
         run:()=> {
           const x=val("t21");
           const trimmed=x.trim();
           if(trimmed!=="" && !isNaN(trimmed)){
             const n=Number(trimmed);
             return Numeric detected:\\nSquare = ${n*n}\\nDouble = ${n*2};
           }
           return String detected:\\nUpper = "${x.toUpperCase()}"\\nLength = ${x.length};
         }
    },
    22:{ title:"Validate: password min length rule",
         desc:"Enter password. Must be at least 6 chars.",
         inputs:[{id:"t22", ph:"Password (min 6)"}],
         run:()=> val("t22").length>=6 ? "Password valid ✅" : "Password invalid ❌ (min 6 chars)"
    },
    23:{ title:"Validate: required field not empty",
         desc:"Enter any required field.",
         inputs:[{id:"t23", ph:"Required field"}],
         run:()=> val("t23").trim()!=="" ? "OK ✅" : "Required ❌ (cannot be empty)"
    },
    24:{ title:"Signup form with submit/reset (demo)",
         desc:"Enter name, email, password and run.",
         inputs:[
           {id:"t24name", ph:"Name"},
           {id:"t24email", ph:"Email"},
           {id:"t24pwd", ph:"Password"}
         ],
         run:()=> Submitted:\\nName: ${val("t24name")}\\nEmail: ${val("t24email")}\\nPassword: ${"*".repeat(val("t24pwd").length)}
    },
    25:{ title:"Display form output in a table format",
         desc:"Enter name & email, output shows table text.",
         inputs:[
           {id:"t25name", ph:"Name"},
           {id:"t25email", ph:"Email"}
         ],
         run:()=> {
           const name=val("t25name").trim(), email=val("t25email").trim();
           if(!name || !email) return "Enter name and email.";
           return TABLE:\\n| Name | Email |\\n| ${name} | ${email} |;
         }
    },
    26:{ title:"Clear output on reset",
         desc:"Click top Reset button to clear output + inputs + saved array.",
         inputs:[],
         run:()=> "Use top Reset button ✅"
    },
    27:{ title:"Create person object and update age using input",
         desc:"Enter name, age, new age.",
         inputs:[
           {id:"t27name", ph:"Name (e.g. Ali)"},
           {id:"t27age", ph:"Age (e.g. 20)"},
           {id:"t27new", ph:"New age (e.g. 22)"}
         ],
         run:()=> {
           const name=val("t27name").trim()||"Unknown";
           const age=Number(val("t27age"));
           const newAge=Number(val("t27new"));
           if(Number.isNaN(age)||Number.isNaN(newAge)) return "Enter valid ages.";
           let person={name, age};
           person.age=newAge;
           return Person object:\\n${JSON.stringify(person,null,2)};
         }
    },
    28:{ title:"Display object after update",
         desc:"Same output as task 27 (shows updated object).",
         inputs:[],
         run:()=> "Open Task 27 and run — it displays updated object ✅"
    },
    29:{ title:"Countdown timer using setInterval (Start/Pause/Reset)",
         desc:"Enter start seconds, then use Start/Pause/Reset buttons.",
         inputs:[{id:"t29", ph:"Start seconds (e.g. 10)"}],
         extraButtons:[
           {text:"Start", action:"timerStart"},
           {text:"Pause", action:"timerPause"},
           {text:"Reset", action:"timerReset"}
         ],
         run:()=> "Use Start/Pause/Reset buttons above ✅"
    },
    30:{ title:"Hover event: change button style on mouse over",
         desc:"Hover the button below to see style change.",
         inputs:[{id:"t30demo", type:"hoverBtn"}],
         run:()=> "Hover the button ✅"
    },
  };

  function openTask(){
    const n = Number($("taskSelect").value);
    if(!n) return;
    renderTask(n);
  }

  function renderTask(n){
    const t = TASKS[n];
    $("taskCard").classList.remove("hide");
    $("taskHeading").textContent = ${n}) ${t.title};
    $("taskDesc").textContent = t.desc;
    $("taskBadge").textContent = Task ${n};

    // inputs
    const area = $("inputsArea");
    area.innerHTML = "";

    (t.inputs || []).forEach(inp=>{
      if(inp.type === "select"){
        const sel=document.createElement("select");
        sel.id=inp.id;
        inp.options.forEach(op=>{
          const o=document.createElement("option");
          o.value=op; o.textContent=op;
          sel.appendChild(o);
        });
        area.appendChild(sel);
        return;
      }
      if(inp.type === "hoverBtn"){
        const btn=document.createElement("button");
        btn.textContent="Hover Me";
        btn.style.background = "var(--pink)";
        btn.style.color = "#fff";
        btn.onmouseover = ()=>{ btn.style.background="#1e1e1e"; $("output").textContent="Mouse over ✅ style changed."; };
        btn.onmouseout  = ()=>{ btn.style.background="var(--pink)"; $("output").textContent="Mouse out ✅ style restored."; };
        area.appendChild(btn);
        return;
      }
      const input=document.createElement("input");
      input.id=inp.id;
      input.placeholder=inp.ph || "";
      area.appendChild(input);
    });

    // buttons
    const btnArea = $("buttonsArea");
    btnArea.innerHTML = "";

    const runBtn = document.createElement("button");
    runBtn.textContent = "Run";
    runBtn.onclick = runTask;
    btnArea.appendChild(runBtn);

    (t.extraButtons || []).forEach(b=>{
      const btn = document.createElement("button");
      btn.textContent = b.text;
      btn.onclick = () => handleExtraAction(b.action);
      btnArea.appendChild(btn);
    });

    $("output").textContent = "Output will appear here.";
    stopTimer(); // safety when switching tasks
  }

  function runTask(){
    const n = Number($("taskSelect").value);
    if(!n) return;
    try{
      const res = TASKS[n].run();
      if(typeof res === "string") $("output").textContent = res;
    }catch(e){
      $("output").textContent = "Error: " + e.message;
    }
  }

  function handleExtraAction(action){
    if(action === "saveArray"){
      const input = val("t7");
      if(!input.trim()) return $("output").textContent = "Enter values first (e.g. a,b,c).";
      savedArray = input.split(",").map(x=>x.trim());
      $("output").textContent = Array saved ✅: [${savedArray.join(", ")}];
      return;
    }
    if(action === "timerStart"){ timerStart(); return; }
    if(action === "timerPause"){ timerPause(); return; }
    if(action === "timerReset"){ timerReset(); return; }
  }

  // Navigation
  function nextTask(){
    let n = Number($("taskSelect").value) || 1;
    if(n < 30) n++;
    $("taskSelect").value = String(n);
    renderTask(n);
  }
  function prevTask(){
    let n = Number($("taskSelect").value) || 1;
    if(n > 1) n--;
    $("taskSelect").value = String(n);
    renderTask(n);
  }
  function backToMenu(){
    stopTimer();
    $("taskCard").classList.add("hide");
    $("output").textContent = "";
    window.scrollTo({top:0, behavior:"smooth"});
  }

  function resetAll(){
    stopTimer();
    savedArray = [];
    $("inputsArea").querySelectorAll("input").forEach(i=>i.value="");
    $("output").textContent = "Cleared ✅";
  }

  function val(id){ return $(id) ? $(id).value : ""; }
  function toNums(s){
    if(!s) return [];
    return s.split(",").map(x=>Number(x.trim())).filter(x=>!Number.isNaN(x));
  }

  // Timer (Task 29)
  function renderTimer(){
    $("output").textContent = Time: ${timerT}s;
  }
  function timerStart(){
    if(timerT === 0){
      const start = Number(val("t29"));
      if(Number.isNaN(start) || start < 0){
        $("output").textContent = "Enter start seconds (>=0).";
        return;
      }
      timerT = start;
      timerInitial = start;
      renderTimer();
    }
    if(timerId) return;
    timerId = setInterval(()=>{
      if(timerT <= 0){
        stopTimer();
        $("output").textContent = "Done ✅";
        return;
      }
      timerT--;
      renderTimer();
    }, 1000);
  }
  function timerPause(){
    stopTimer();
    renderTimer();
  }
  function timerReset(){
    stopTimer();
    timerT = timerInitial;
    renderTimer();
  }
  function stopTimer(){
    if(timerId){ clearInterval(timerId); timerId = null; }
  }

  // Auto open on change
  $("taskSelect").addEventListener("change", openTask);
</script>
</body>
</html>
