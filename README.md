<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Order Search</title>
</head>
<body>
    <input type="text" id="trackingNumber" placeholder="Enter tracking number">
    <button onclick="searchOrder()">Search</button>
    <div id="result" style="display: none;"></div>

    <script src="https://apis.google.com/js/api.js"></script>
    <script>
        const SPREADSHEET_ID = '1Xx-UhRa1QtGA6yx6FMDw68zWb8iOoWfAi9oIsAaUcKo';
        const API_KEY = 'AIzaSyBKGVApvBtnGSO6IIJJloiO5GzOP1jHyiE'; // Replace with your Google API key

        function searchOrder() {
            const trackingNumber = document.getElementById('trackingNumber').value;
            const resultDiv = document.getElementById('result');
            resultDiv.style.display = 'none'; 

            // Load the Sheets API
            gapi.load('client', () => {
                gapi.client.init({
                    apiKey: API_KEY,
                    discoveryDocs: ["https://sheets.googleapis.com/$discovery/rest?version=v4"],
                }).then(() => {
                    return gapi.client.sheets.spreadsheets.values.get({
                        spreadsheetId: SPREADSHEET_ID,
                        range: 'Sheet1!A2:D', // Adjust the range according to your data
                    });
                }).then(response => {
                    const data = response.result.values;
                    let resultHtml = '';

                    const orderData = data.find(row => row[0] === trackingNumber); // Assuming tracking number is in the first column

                    if (orderData) {
                        const reshipTrackingNumber = orderData[1]; // Replace with actual column index for reshipTrackingNumber
                        const ddlForReshipping = orderData[2]; // Replace with actual column index for ddlForReshipping
                        const returnToSender = orderData[3]; // Replace with actual column index for returnToSender

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
                }).catch(error => {
                    console.error('Error fetching data from Google Sheets:', error);
                    resultDiv.innerHTML = '<p>There was an error retrieving your order information. Please try again later.</p>';
                    resultDiv.style.display = 'block';
                });
            });
        }
    </script>
</body>
</html>
