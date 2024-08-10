# yasseroo9.github.io
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order Tracking</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #f4f4f4;
        }
        .container {
            max-width: 800px;
            margin: 0 auto;
            background: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        h1 {
            text-align: center;
        }
        .search-bar {
            display: flex;
            justify-content: center;
            margin-bottom: 20px;
        }
        .search-bar input {
            width: 80%;
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ddd;
            border-radius: 4px 0 0 4px;
        }
        .search-bar button {
            padding: 10px 20px;
            font-size: 16px;
            border: 1px solid #ddd;
            border-radius: 0 4px 4px 0;
            background-color: #007bff;
            color: #fff;
            cursor: pointer;
        }
        .search-bar button:hover {
            background-color: #0056b3;
        }
        .result {
            display: none;
        }
        .result p {
            font-size: 18px;
            line-height: 1.6;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Order Tracking</h1>
        <div class="search-bar">
            <input type="text" id="trackingNumber" placeholder="Enter Tracking Number or Order ID">
            <button onclick="searchOrder()">Search</button>
        </div>
        <div id="result" class="result">
            <!-- Result content will be injected here -->
        </div>
    </div>

    <script>
        function searchOrder() {
            const trackingNumber = document.getElementById('trackingNumber').value;
            const resultDiv = document.getElementById('result');

            // Example of result processing, in a real scenario this would be handled by a backend API
            fetch(`https://your-backend-api.com/search?trackingNumber=${trackingNumber}`)
                .then(response => response.json())
                .then(data => {
                    let resultHtml = '';

                    if (data.reshipTrackingNumber) {
                        resultHtml += `<p>Your Reship Tracking Number: ${data.reshipTrackingNumber}</p>`;
                        // Add country check and message display logic here
                    } else if (data.ddlForReshipping) {
                        if (new Date() > new Date(data.ddlForReshipping)) {
                            resultHtml += `<p>Refund initiation script: ${data.refundDetails}</p>`;
                        }
                    } else if (data.returnToSender) {
                        resultHtml += `<p>Package is returning to sender. Apologies for the inconvenience. We will reship within 14-35 days.</p>`;
                    }

                    resultDiv.innerHTML = resultHtml;
                    resultDiv.style.display = 'block';
                })
                .catch(error => {
                    resultDiv.innerHTML = '<p>There was an error retrieving your order information. Please try again later.</p>';
                    resultDiv.style.display = 'block';
                });
        }
    </script>
</body>
</html>
