<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<title>Financial Dashboard</title>
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">

<style>
:root{
    --primary:#1f2937;
    --secondary:#374151;
    --accent:#4f46e5;
    --success:#16a34a;
    --danger:#dc2626;
    --bg:#f3f4f6;
    --card:#ffffff;
    --border:#e5e7eb;
}

*{
    box-sizing:border-box;
    font-family:"Inter", sans-serif;
}

body{
    background:var(--bg);
    margin:0;
    padding:30px;
}

.container{
    max-width:900px;
    margin:auto;
}

.header{
    background:linear-gradient(135deg,#4f46e5,#4338ca);
    color:#fff;
    padding:25px;
    border-radius:16px;
    margin-bottom:25px;
}

.header h1{
    margin:0;
    font-size:26px;
    font-weight:700;
}

.card{
    background:var(--card);
    border-radius:14px;
    padding:20px;
    margin-bottom:20px;
    box-shadow:0 10px 25px rgba(0,0,0,.05);
}

.card h2{
    margin:0 0 15px;
    font-size:18px;
    color:var(--primary);
}

.grid{
    display:grid;
    grid-template-columns:2fr 1fr;
    gap:10px;
}

input{
    width:100%;
    padding:12px;
    border-radius:10px;
    border:1px solid var(--border);
    font-size:15px;
}

input:focus{
    outline:none;
    border-color:var(--accent);
}

button{
    padding:12px;
    border:none;
    border-radius:10px;
    background:var(--accent);
    color:#fff;
    font-size:15px;
    cursor:pointer;
}

button:hover{
    opacity:.9;
}

.list{
    margin-top:10px;
}

.item{
    display:grid;
    grid-template-columns:1fr auto auto;
    gap:10px;
    align-items:center;
    padding:10px;
    border-bottom:1px solid var(--border);
}

.item:last-child{
    border-bottom:none;
}

.amount{
    font-weight:600;
}

.delete-btn{
    background:var(--danger);
    padding:6px 10px;
    font-size:13px;
    border-radius:8px;
}

.result{
    display:grid;
    grid-template-columns:1fr 1fr 1fr;
    gap:15px;
}

.stat{
    background:#f9fafb;
    padding:15px;
    border-radius:12px;
    text-align:center;
}

.stat h3{
    margin:0;
    font-size:14px;
    color:#6b7280;
}

.stat p{
    margin:8px 0 0;
    font-size:20px;
    font-weight:700;
}

.positive{
    color:var(--success);
}

.negative{
    color:var(--danger);
}

@media(max-width:768px){
    .grid,.result{
        grid-template-columns:1fr;
    }
}
</style>
</head>

<body>

<div class="container">

    <div class="header">
        <h1>💼 Financial Expense Dashboard</h1>
    </div>

    <div class="card">
        <h2>💰 المعاش الشهري</h2>
        <input type="number" id="salary" placeholder="أدخل المعاش">
        <button onclick="saveSalary()">حفظ</button>
    </div>

    <div class="card">
        <h2>📌 مصاريف ثابتة</h2>
        <div class="grid">
            <input type="text" id="fixedName" placeholder="اسم المصروف">
            <input type="number" id="fixedAmount" placeholder="القيمة">
        </div>
        <button onclick="addFixed()">إضافة</button>
        <div class="list" id="fixedList"></div>
    </div>

    <div class="card">
        <h2>🧾 مصاريف متغيرة</h2>
        <div class="grid">
            <input type="text" id="varName" placeholder="اسم المصروف">
            <input type="number" id="varAmount" placeholder="القيمة">
        </div>
        <button onclick="addVariable()">إضافة</button>
        <div class="list" id="varList"></div>
    </div>

    <div class="card result">
        <div class="stat">
            <h3>المصاريف الثابتة</h3>
            <p id="fixedTotal">0</p>
        </div>
        <div class="stat">
            <h3>المصاريف المتغيرة</h3>
            <p id="varTotal">0</p>
        </div>
        <div class="stat">
            <h3>المتبقي</h3>
            <p id="remaining">0</p>
        </div>
    </div>

</div>

<script>
let salary = Number(localStorage.getItem("salary")) || 0;
let fixedExpenses = JSON.parse(localStorage.getItem("fixed")) || [];
let variableExpenses = JSON.parse(localStorage.getItem("variable")) || [];

salaryInput.value = salary;

function saveSalary(){
    salary = Number(salaryInput.value);
    localStorage.setItem("salary", salary);
    calculate();
}

function addFixed(){
    if(fixedName.value && fixedAmount.value){
        fixedExpenses.push({name:fixedName.value,amount:Number(fixedAmount.value)});
        fixedName.value="";
        fixedAmount.value="";
        save();
    }
}

function addVariable(){
    if(varName.value && varAmount.value){
        variableExpenses.push({name:varName.value,amount:Number(varAmount.value)});
        varName.value="";
        varAmount.value="";
        save();
    }
}

function deleteFixed(i){
    fixedExpenses.splice(i,1);
    save();
}

function deleteVariable(i){
    variableExpenses.splice(i,1);
    save();
}

function save(){
    localStorage.setItem("fixed",JSON.stringify(fixedExpenses));
    localStorage.setItem("variable",JSON.stringify(variableExpenses));
    render();
}

function render(){
    fixedList.innerHTML="";
    varList.innerHTML="";

    fixedExpenses.forEach((e,i)=>{
        fixedList.innerHTML+=`
        <div class="item">
            <div>${e.name}</div>
            <div class="amount">${e.amount}</div>
            <button class="delete-btn" onclick="deleteFixed(${i})">حذف</button>
        </div>`;
    });

    variableExpenses.forEach((e,i)=>{
        varList.innerHTML+=`
        <div class="item">
            <div>${e.name}</div>
            <div class="amount">${e.amount}</div>
            <button class="delete-btn" onclick="deleteVariable(${i})">حذف</button>
        </div>`;
    });

    calculate();
}

function calculate(){
    let f = fixedExpenses.reduce((s,e)=>s+e.amount,0);
    let v = variableExpenses.reduce((s,e)=>s+e.amount,0);
    let r = salary - f - v;

    fixedTotal.textContent = f;
    varTotal.textContent = v;
    remaining.textContent = r;
    remaining.className = r < 0 ? "negative" : "positive";
}

render();
</script>

</body>
</html>
