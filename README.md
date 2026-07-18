<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Expense Calculator Dashboard</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #f4f6f9;
            color: #333;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 1000px;
            margin: 0 auto;
            background: #ffffff;
            padding: 25px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
        }
        .header-flex {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 25px;
            border-bottom: 2px solid #edf2f7;
            padding-bottom: 15px;
        }
        .title-area h1 {
            margin: 0 0 5px 0;
            font-size: 24px;
            color: #1a202c;
        }
        .date-badge {
            font-size: 14px;
            color: #718096;
            font-weight: 500;
        }
        .summary-box {
            background: #f7fafc;
            border: 1px solid #e2e8f0;
            border-radius: 6px;
            padding: 15px;
            min-width: 280px;
        }
        .summary-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 8px;
            font-size: 14px;
        }
        .summary-row:last-child {
            margin-bottom: 0;
            font-weight: bold;
            border-top: 1px dashed #cbd5e0;
            padding-top: 8px;
            color: #2b6cb0;
        }
        .summary-row input {
            width: 90px;
            text-align: right;
            font-weight: bold;
            border: 1px solid #cbd5e0;
            border-radius: 4px;
            padding: 2px 5px;
        }
        .action-bar {
            margin-bottom: 20px;
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            align-items: center;
        }
        .btn {
            padding: 10px 20px;
            font-size: 14px;
            font-weight: 600;
            border-radius: 5px;
            cursor: pointer;
            border: none;
            transition: background 0.2s;
            white-space: nowrap;
        }
        .btn-save {
            background-color: #3182ce;
            color: white;
        }
        .btn-save:hover { background-color: #2b6cb0; }
        
        .whatsapp-group {
            display: flex;
            align-items: center;
            gap: 5px;
            flex-wrap: nowrap;
        }
        .phone-input {
            padding: 9px 12px;
            font-size: 14px;
            border: 1px solid #cbd5e0;
            border-radius: 5px;
            width: 150px;
            box-sizing: border-box;
        }
        .btn-whatsapp {
            background-color: #25D366;
            color: white;
        }
        .btn-whatsapp:hover { background-color: #1ebd59; }

        .btn-print {
            background-color: #4a5568;
            color: white;
        }
        .btn-print:hover { background-color: #2d3748; }
        
        .btn-add {
            background-color: #38a169;
            color: white;
            padding: 6px 12px;
            font-size: 12px;
            margin-left: auto;
        }
        .btn-add:hover { background-color: #2f855a; }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-top: 10px;
        }
        th {
            background-color: #edf2f7;
            color: #4a5568;
            font-weight: 600;
            text-align: left;
            padding: 12px 10px;
            font-size: 14px;
            border-bottom: 2px solid #cbd5e0;
        }
        td {
            padding: 8px 10px;
            border-bottom: 1px solid #e2e8f0;
        }
        input[type="text"], input[type="date"], input[type="number"], select {
            width: 100%;
            box-sizing: border-box;
            padding: 6px;
            border: 1px solid #e2e8f0;
            border-radius: 4px;
            font-size: 14px;
        }
        input:focus, select:focus {
            outline: none;
            border-color: #3182ce;
            box-shadow: 0 0 0 3px rgba(66, 153, 225, 0.15);
        }
        .read-only-cell {
            background-color: #f7fafc;
            font-weight: 500;
            color: #4a5568;
        }
        .cr-text { color: #38a169; font-weight: bold; }
        .dr-text { color: #e53e3e; font-weight: bold; }

        /* Print Specific Styling */
        @media print {
            body { background: white; padding: 0; }
            .container { box-shadow: none; padding: 0; max-width: 100%; }
            .action-bar, .btn-add, .delete-col, .whatsapp-group { display: none !important; }
            input, select {
                border: none !important;
                background: transparent !important;
                appearance: none;
                -webkit-appearance: none;
            }
            .read-only-cell { background: transparent !important; }
        }
    </style>
</head>
<body>

<div class="container">
    <div class="header-flex">
        <div class="title-area">
            <div class="date-badge">Date: <span id="current-date"></span></div>
        </div>
        <div class="summary-box">
            <div class="summary-row">
                <span>Opening Balance:</span>
                <input type="number" id="opening-balance" value="10000" oninput="calculateSheet()">
            </div>
            <div class="summary-row">
                <span>Total Expenses:</span>
                <span id="total-expenses">0.00</span>
            </div>
            <div class="summary-row">
                <span>Total Due:</span>
                <span id="total-due">10000.00</span>
            </div>
        </div>
    </div>

    <div class="action-bar">
        <button class="btn btn-save" onclick="saveData()">Save Changes</button>
        <div class="whatsapp-group">
            <button class="btn btn-whatsapp" onclick="sendWhatsApp()">Send to WhatsApp</button>
            <input type="text" id="target-phone" class="phone-input" value="+91 9875320978" placeholder="Phone with country code">
        </div>
        <button class="btn btn-print" onclick="window.print()">Print Page</button>
        <button class="btn btn-add" onclick="addRow()">+ Add Row</button>
    </div>

    <table id="expense-table">
        <thead>
            <tr>
                <th style="width: 6%;">Sl No</th>
                <th style="width: 15%;">Name</th>
                <th style="width: 15%;">Date</th>
                <th style="width: 20%;">Particulars</th>
                <th style="width: 12%;">Dropdown</th>
                <th style="width: 12%;">Enter Amount</th>
                <th style="width: 12%;">Deposits/Withdrawals</th>
                <th style="width: 8%;">CR/DR</th>
                <th class="delete-col" style="width: 5%;"></th>
            </tr>
        </thead>
        <tbody id="table-body">
            <!-- Rows injected dynamically via JavaScript -->
        </tbody>
    </table>
</div>

<script>
    document.getElementById('current-date').innerText = new Date().toISOString().split('T')[0];

    let initialData = [
        { name: "Kapil", date: "2026-07-15", particular: "Medicine", type: "Debit", amount: 400 },
        { name: "Kapil", date: "2026-07-15", particular: "Phone Recharge", type: "Credit", amount: 300 },
        { name: "Kapil", date: "2026-07-15", particular: "Doctors bill", type: "Select any", amount: "" }
    ];

    let savedData = localStorage.getItem('expense_data');
    let savedBalance = localStorage.getItem('opening_balance');
    let savedPhone = localStorage.getItem('target_phone');
    
    let tableData = savedData ? JSON.parse(savedData) : initialData;
    if (savedBalance) document.getElementById('opening-balance').value = savedBalance;
    if (savedPhone) document.getElementById('target-phone').value = savedPhone;

    function renderTable() {
        const tbody = document.getElementById('table-body');
        tbody.innerHTML = '';
        
        tableData.forEach((row, index) => {
            const tr = document.createElement('tr');
            tr.innerHTML = `
                <td class="read-only-cell">${index + 1}</td>
                <td><input type="text" value="${row.name}" oninput="updateData(${index}, 'name', this.value)"></td>
                <td><input type="date" value="${row.date}" oninput="updateData(${index}, 'date', this.value)"></td>
                <td><input type="text" value="${row.particular}" oninput="updateData(${index}, 'particular', this.value)"></td>
                <td>
                    <select onchange="updateData(${index}, 'type', this.value); calculateSheet();">
                        <option value="Select any" ${row.type === 'Select any' ? 'selected' : ''}>Select any</option>
                        <option value="Debit" ${row.type === 'Debit' ? 'selected' : ''}>Debit</option>
                        <option value="Credit" ${row.type === 'Credit' ? 'selected' : ''}>Credit</option>
                    </select>
                </td>
                <td><input type="number" value="${row.amount}" oninput="updateData(${index}, 'amount', this.value); calculateSheet();"></td>
                <td class="read-only-cell math-deposit" id="deposit-${index}"></td>
                <td class="read-only-cell math-crdr" id="crdr-${index}"></td>
                <td class="delete-col"><button onclick="deleteRow(${index})" style="background:none; border:none; color:#e53e3e; cursor:pointer; font-weight:bold;">✕</button></td>
            `;
            tbody.appendChild(tr);
        });
        calculateSheet();
    }

    function updateData(index, field, value) {
        tableData[index][field] = value;
    }

    function addRow() {
        tableData.push({ name: "", date: new Date().toISOString().split('T')[0], particular: "", type: "Select any", amount: "" });
        renderTable();
    }

    function deleteRow(index) {
        tableData.splice(index, 1);
        renderTable();
    }

    function calculateSheet() {
        const openingBalance = parseFloat(document.getElementById('opening-balance').value) || 0;
        let runningBalance = openingBalance;
        let totalExpenses = 0;

        tableData.forEach((row, index) => {
            const type = row.type;
            const amount = parseFloat(row.amount) || 0;
            
            const depositCell = document.getElementById(`deposit-${index}`);
            const crdrCell = document.getElementById(`crdr-${index}`);
            
            if (type === "Select any" || !row.amount) {
                if(depositCell && crdrCell) {
                    depositCell.innerText = "";
                    crdrCell.innerText = "";
                    crdrCell.className = "read-only-cell math-crdr";
                }
                return;
            }

            if (type === "Debit") {
                runningBalance -= amount;
                totalExpenses += amount;
                if(crdrCell) {
                    crdrCell.innerText = "DR";
                    crdrCell.className = "read-only-cell math-crdr dr-text";
                }
            } else if (type === "Credit") {
                runningBalance += amount;
                if(crdrCell) {
                    crdrCell.innerText = "CR";
                    crdrCell.className = "read-only-cell math-crdr cr-text";
                }
            }
            
            if(depositCell) depositCell.innerText = runningBalance.toFixed(2);
        });

        document.getElementById('total-expenses').innerText = totalExpenses.toFixed(2);
        document.getElementById('total-due').innerText = runningBalance.toFixed(2);
    }

    function saveData() {
        localStorage.setItem('expense_data', JSON.stringify(tableData));
        localStorage.setItem('opening_balance', document.getElementById('opening-balance').value);
        localStorage.setItem('target_phone', document.getElementById('target-phone').value);
        alert('All changes saved successfully!');
    }

    function sendWhatsApp() {
        localStorage.setItem('expense_data', JSON.stringify(tableData));
        localStorage.setItem('opening_balance', document.getElementById('opening-balance').value);
        localStorage.setItem('target_phone', document.getElementById('target-phone').value);

        const openBal = document.getElementById('opening-balance').value;
        const totExp = document.getElementById('total-expenses').innerText;
        const totDue = document.getElementById('total-due').innerText;
        const todayStr = document.getElementById('current-date').innerText;
        
        let rawPhone = document.getElementById('target-phone').value;
        let cleanPhone = rawPhone.replace(/\D/g, ''); 

        if(!cleanPhone) {
            alert("Please enter a valid phone number.");
            return;
        }

        // Exact header layout request
        let message = `*EXPENSE REPORT - ${todayStr}*\n`;
        message += `--------------------------------\n`;
        message += `Opening Bal: Rs ${openBal}\n`;
        message += `Total Exp: Rs ${totExp}\n`;
        message += `Total Due: Rs ${totDue}\n`;
        message += `--------------------------------\n\n`;
        message += `*TRANSACTION DETAILS:*\n`;

        // Fixed-width text padding function to hold grid shape perfectly inside mobile/PC viewports
        const pad = (text, length) => {
            let str = String(text || '-');
            return str.padEnd(length, ' ').substring(0, length);
        };

        // Monospace block creates the clean Excel structure directly beneath the text headers
        message += `\`\`\`\n`;
        message += `${pad("SN", 2)}| ${pad("Name", 6)}| ${pad("Particulars", 11)}| ${pad("Amt", 5)}| ${pad("Type", 2)}\n`;
        message += `--|-------|------------|-----|--\n`;

        tableData.forEach((row, idx) => {
            if (row.name || row.particular || row.amount) {
                const sn = pad(idx + 1, 2);
                const name = pad(row.name, 6);
                const part = pad(row.particular, 11);
                const amt = pad(row.amount, 5);
                const type = pad(row.type === 'Credit' ? 'CR' : row.type === 'Debit' ? 'DR' : '-', 2);
                
                message += `${sn}| ${name}| ${part}| ${amt}| ${type}\n`;
            }
        });
        message += `\`\`\``;

        const encodedMessage = encodeURIComponent(message);
        const url = `https://api.whatsapp.com/send?phone=${cleanPhone}&text=${encodedMessage}`;
        
        window.open(url, '_blank');
    }

    renderTable();
</script>

</body>
</html>
