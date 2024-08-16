<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order Management System</title>
    <script src="https://cdn.sheetjs.com/xlsx-latest/package/dist/xlsx.full.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            color: #333;
            margin: 0;
            padding: 0;
        }
        h1, h2 {
            text-align: center;
            color: #2c3e50;
        }
        section {
            background-color: #fff;
            margin: 20px auto;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
            max-width: 800px;
        }
        label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }
        input[type="text"], input[type="file"] {
            width: calc(100% - 20px);
            padding: 10px;
            margin-bottom: 20px;
            border: 1px solid #ccc;
            border-radius: 4px;
            font-size: 16px;
        }
        button {
            display: inline-block;
            padding: 10px 20px;
            margin-right: 10px;
            background-color: #3498db;
            color: #fff;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #2980b9;
        }
        button:disabled {
            background-color: #bdc3c7;
            cursor: not-allowed;
        }
        hr {
            border: 0;
            border-top: 1px solid #eee;
            margin: 40px 0;
        }
        #result, #dataDisplay {
            margin-top: 20px;
        }
        #result p, #dataDisplay table {
            font-size: 16px;
        }
        #dataDisplay table {
            width: 100%;
            border-collapse: collapse;
        }
        #dataDisplay th, #dataDisplay td {
            padding: 10px;
            text-align: left;
            border: 1px solid #ddd;
        }
        #dataDisplay th {
            background-color: #3498db;
            color: #fff;
        }
        #dataDisplay td {
            background-color: #f9f9f9;
        }
    </style>
</head>
<body>
    <h1>Order Management System</h1>

    <!-- Admin Section for uploading XLSX -->
    <section id="adminSection">
        <h2>Admin Panel</h2>
        <label for="fileUpload">Upload Order File (XLSX):</label>
        <input type="file" id="fileUpload" accept=".xlsx">
        <button onclick="saveData()">Upload and Save Data</button>
        <button onclick="removeData()">Remove All Data</button>
    </section>

    <hr>

    <!-- Section for searching orders -->
    <section id="searchSection">
        <h2>Search Your Order</h2>
        <label for="trackingNumber">Enter Tracking Number or Order ID:</label>
        <input type="text" id="trackingNumber">
        <button onclick="searchOrder()">Search</button>
        <div id="result" style="display:none;"></div>
    </section>

    <hr>

    <!-- Section for displaying all data -->
    <section id="dataSection">
        <h2>All Order Data</h2>
        <button onclick="displayAllData()">Display All Data</button>
        <div id="dataDisplay" style="display:none;"></div>
    </section>

    <script>
        let orderData = [];

        document.getElementById('fileUpload').addEventListener('change', function(event) {
            const file = event.target.files[0];
            const reader = new FileReader();

            reader.onload = function(e) {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, {type: 'array'});
                const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
                orderData = XLSX.utils.sheet_to_json(firstSheet, {header: 1});
            };

            reader.readAsArrayBuffer(file);
        });

        function saveData() {
            if (orderData.length > 0) {
                localStorage.setItem('orderData', JSON.stringify(orderData));
                alert('Data saved successfully!');
            } else {
                alert('Please upload an XLSX file first.');
            }
        }

        function loadData() {
            const savedData = localStorage.getItem('orderData');
            if (savedData) {
                orderData = JSON.parse(savedData);
            }
        }

        function removeData() {
            localStorage.removeItem('orderData');
            orderData = [];
            alert('All data has been removed.');
            document.getElementById('dataDisplay').style.display = 'none';
        }

        function searchOrder() {
            loadData();
            const trackingNumber = document.getElementById('trackingNumber').value;
            const resultDiv = document.getElementById('result');
            resultDiv.style.display = 'none';

            let resultHtml = '';

            const order = orderData.find(row => row[1] === trackingNumber || row[2] === trackingNumber); // Check both "Tracking No." (index 1) and "Order ID" (index 2)

            if (order) {
                const reshipTrackingNumber = order[13]; // "Reship Tracking Number"
                const ddlForReshipping = order[5]; // "DDL For Re-shipping"
                const returnToSender = order[4]; // "RTO reason feedback..."

                if (reshipTrackingNumber) {
                    // Copy the Reship Tracking Number to the search bar and generate the response script
                    document.getElementById('trackingNumber').value = reshipTrackingNumber;
                    resultHtml += `
                        <p>Dear customer, for your order ${trackingNumber} logistics track has been updated with the following: In international transit.</p>
                        <p>You can check your order logistics track on this website: <a href="https://www.17track.net/en" target="_blank">https://www.17track.net/en</a></p>
                        <p>This is the tracking number: ${reshipTrackingNumber}</p>
                        <p>The estimated time of arrival of your order is:</p>
                    `;
                } else if (ddlForReshipping && new Date() > new Date(ddlForReshipping)) {
                    // Generate the script when the DDL for Re-shipping is exceeded and no Reship Tracking Number
                    resultHtml += `
                        <p>I'm truly sorry for the inconvenience caused to you. Please don't worry, we will escalate your dispute to the concerned department, and you can expect to receive the result within 72 hours.</p>
                        <p>Thank you for your understanding. If you have any questions, please feel free to contact us at any time. 🕒📲</p>
                    `;
                } else {
                    // Generate the script when the order is returning to the sender
                    resultHtml += `
                        <p>We regret to inform you that your order has been returned to our warehouse due to ${returnToSender}. Please note that we will arrange the reshipment for you within 7 days.</p>
                        <p>Kindly provide your address and if there are any changes needed for the recipient or mobile number, please provide them as well. Thank you in advance.</p>
                        <p>Please note that you will receive an SMS or WhatsApp including the new tracking number to track your order when we reship it. Thank you for your understanding.</p>
                    `;
                }
            } else {
                resultHtml = '<p>No matching order found.</p>';
            }

            resultDiv.innerHTML = resultHtml;
            resultDiv.style.display = 'block';
        }

        function displayAllData() {
            loadData();
            const dataDisplay = document.getElementById('dataDisplay');
            dataDisplay.style.display = 'block';

            let dataHtml = '<table border="1"><tr><th>Date</th><th>Tracking No.</th><th>Order ID</th><th>Country</th><th>RTO reason feedback</th><th>DDL For Re-shipping</th><th>Register Date</th><th>Recipient\'s Name</th><th>Street</th><th>City</th><th>State</th><th>Zip Code</th><th>Phone Number</th><th>Reship Tracking Number</th><th>Reship Date</th></tr>';

            orderData.forEach(row => {
                dataHtml += '<tr>';
                row.forEach(cell => {
                    dataHtml += `<td>${cell}</td>`;
                });
                dataHtml += '</tr>';
            });

            dataHtml += '</table>';
            dataDisplay.innerHTML = dataHtml;
        }
    </script>
</body>
</html>
