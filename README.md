<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order Search</title>
</head>
<body>
    <h1>Search Your Order</h1>

    <label for="fileUpload">Upload Order File (CSV): </label>
    <input type="file" id="fileUpload" accept=".csv">

    <label for="trackingNumber">Enter Tracking Number: </label>
    <input type="text" id="trackingNumber">

    <button onclick="searchOrder()">Search</button>

    <div id="result" style="display:none;"></div>

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

        function searchOrder() {
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
    </script>
</body>
</html>
