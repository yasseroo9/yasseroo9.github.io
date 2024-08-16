<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order Search Admin Panel</title>
</head>
<body>
    <h1>Order Management System</h1>

    <!-- Admin Section for uploading CSV -->
    <section id="adminSection">
        <h2>Admin Panel</h2>
        <label for="fileUpload">Upload Order File (CSV): </label>
        <input type="file" id="fileUpload" accept=".csv">
        <button onclick="saveData()">Upload and Save Data</button>
    </section>

    <hr>

    <!-- Section for searching orders -->
    <section id="searchSection">
        <h2>Search Your Order</h2>
        <label for="trackingNumber">Enter Tracking Number: </label>
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
                const text = e.target.result;
                orderData = parseCSV(text);
            };

            reader.readAsText(file);
        });

        function parseCSV(text) {
            const rows = text.split('\n').map(row => row.split(','));
            return rows;
        }

        function saveData() {
            if (orderData.length > 0) {
                localStorage.setItem('orderData', JSON.stringify(orderData));
                alert('Data saved successfully!');
            } else {
                alert('Please upload a CSV file first.');
            }
        }

        function loadData() {
            const savedData = localStorage.getItem('orderData');
            if (savedData) {
                orderData = JSON.parse(savedData);
            }
        }

        function searchOrder() {
            loadData();
            const trackingNumber = document.getElementById('trackingNumber').value;
            const resultDiv = document.getElementById('result');
            resultDiv.style.display = 'none';

            let resultHtml = '';

            const order = orderData.find(row => row[0] === trackingNumber);

            if (order) {
                const reshipTrackingNumber = order[1];
                const ddlForReshipping = order[2];
                const returnToSender = order[3];

                if (reshipTrackingNumber) {
                    resultHtml += `<p>Your Reship Tracking Number: ${reshipTrackingNumber}</p>`;
                } else if (ddlForReshipping && new Date() > new Date(ddlForReshipping)) {
                    resultHtml += `<p>Refund initiation script based on your policy.</p>`;
                } else if (returnToSender === 'TRUE') {
                    resultHtml += `<p>Package is returning to sender. Apologies for the inconvenience. We will reship within 14-35 days.</p>`;
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

            let dataHtml = '<table border="1"><tr><th>Tracking Number</th><th>Reship Tracking Number</th><th>DDL For Reshipping</th><th>Return To Sender</th></tr>';

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
