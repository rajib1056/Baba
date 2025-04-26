<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>অনুদান সংস্থা হিসাব</title>
    <link rel="stylesheet" href="style.css">
    <link rel="icon" type="image/png" href="favicon.png">
</head>
<body>

    <header>
        <h1>অনুদান সংস্থা হিসাব</h1>
        <button onclick="toggleDarkMode()">ডার্ক মোড চালু/বন্ধ</button>
    </header>

    <section class="input-section">
        <input type="date" id="dateInput">
        <select id="typeInput">
            <option value="donation">অনুদান</option>
            <option value="expense">খরচ</option>
        </select>
        <input type="text" id="descriptionInput" placeholder="বিবরণ">
        <input type="number" id="amountInput" placeholder="পরিমাণ (৳)">
        <button onclick="addRecord()">যোগ করুন</button>
    </section>

    <section class="filter-section">
        <input type="text" id="searchInput" placeholder="অনুসন্ধান করুন..." onkeyup="filterTable()">
        <input type="date" id="filterDate" onchange="filterTable()">
        <select id="filterType" onchange="filterTable()">
            <option value="all">সব দেখান</option>
            <option value="donation">অনুদান</option>
            <option value="expense">খরচ</option>
        </select>
    </section>

    <section id="summary"></section>

    <section class="button-group">
        <button onclick="downloadPDF()">PDF ডাউনলোড</button>
        <button onclick="printRecords()">প্রিন্ট করুন</button>
    </section>

    <table id="recordTable">
        <thead>
            <tr>
                <th>তারিখ</th>
                <th>ধরন</th>
                <th>বিবরণ</th>
                <th>পরিমাণ (৳)</th>
                <th>ডিলিট</th>
            </tr>
        </thead>
        <tbody></tbody>
    </table>

    <script src="script.js"></script>
</body>
</html>
body {
    font-family: 'Segoe UI', sans-serif;
    margin: 0;
    padding: 0;
    background-color: #f4f4f4;
    color: #333;
    text-align: center;
}

header {
    background: #4CAF50;
    color: white;
    padding: 20px;
}

.input-section, .filter-section, .button-group {
    margin: 20px;
}

input, select, button {
    padding: 10px;
    margin: 5px;
    width: 200px;
    border: 1px solid #ccc;
    border-radius: 5px;
}

button {
    background-color: #4CAF50;
    color: white;
    cursor: pointer;
}

button:hover {
    background-color: #45a049;
}

table {
    width: 90%;
    margin: 20px auto;
    border-collapse: collapse;
    background: white;
}

th, td {
    padding: 10px;
    border: 1px solid #ddd;
}

th {
    background: #e2e8f0;
}

.dark-mode {
    background-color: #121212;
    color: #eee;
}
let records = JSON.parse(localStorage.getItem('records')) || [];

function saveRecords() {
    localStorage.setItem('records', JSON.stringify(records));
}

function addRecord() {
    const date = document.getElementById('dateInput').value;
    const type = document.getElementById('typeInput').value;
    const description = document.getElementById('descriptionInput').value;
    const amount = parseFloat(document.getElementById('amountInput').value);

    if (!date || !description || isNaN(amount)) {
        alert('সব তথ্য সঠিকভাবে পূরণ করুন।');
        return;
    }

    records.push({ date, type, description, amount });
    saveRecords();
    displayRecords();
    clearInputs();
}

function clearInputs() {
    document.getElementById('descriptionInput').value = '';
    document.getElementById('amountInput').value = '';
}

function displayRecords() {
    const tbody = document.querySelector('#recordTable tbody');
    tbody.innerHTML = '';
    let totalDonation = 0;
    let totalExpense = 0;

    records.forEach((record, index) => {
        const row = tbody.insertRow();
        row.insertCell(0).innerText = record.date;
        row.insertCell(1).innerText = record.type === 'donation' ? 'অনুদান' : 'খরচ';
        row.insertCell(2).innerText = record.description;
        row.insertCell(3).innerText = record.amount.toFixed(2);
        const delBtn = document.createElement('button');
        delBtn.innerText = 'ডিলিট';
        delBtn.onclick = () => deleteRecord(index);
        row.insertCell(4).appendChild(delBtn);

        if (record.type === 'donation') {
            totalDonation += record.amount;
        } else {
            totalExpense += record.amount;
        }
    });

    document.getElementById('summary').innerHTML = `
        মোট অনুদান: <b>${totalDonation.toFixed(2)}৳</b> | 
        মোট খরচ: <b>${totalExpense.toFixed(2)}৳</b>
    `;
}

function deleteRecord(index) {
    if (confirm('ডিলিট করতে চান?')) {
        records.splice(index, 1);
        saveRecords();
        displayRecords();
    }
}

function filterTable() {
    const searchText = document.getElementById('searchInput').value.toLowerCase();
    const filterDate = document.getElementById('filterDate').value;
    const filterType = document.getElementById('filterType').value;

    const tbody = document.querySelector('#recordTable tbody');
    tbody.innerHTML = '';

    records.forEach((record, index) => {
        if ((filterType === 'all' || record.type === filterType) &&
            (record.description.toLowerCase().includes(searchText) || record.date.includes(searchText)) &&
            (filterDate === '' || record.date === filterDate)) {
            
            const row = tbody.insertRow();
            row.insertCell(0).innerText = record.date;
            row.insertCell(1).innerText = record.type === 'donation' ? 'অনুদান' : 'খরচ';
            row.insertCell(2).innerText = record.description;
            row.insertCell(3).innerText = record.amount.toFixed(2);
            const delBtn = document.createElement('button');
            delBtn.innerText = 'ডিলিট';
            delBtn.onclick = () => deleteRecord(index);
            row.insertCell(4).appendChild(delBtn);
        }
    });
}

function toggleDarkMode() {
    document.body.classList.toggle('dark-mode');
}

function downloadPDF() {
    window.print();
}

function printRecords() {
    window.print();
}

// প্রথম লোডের সময়
displayRecords();

.dark-mode header {
    background: #222;
}

.dark-mode table {
    background: #333;
}
