function searchOrder() {
    const trackingNumber = document.getElementById('trackingNumber').value;
    const resultDiv = document.getElementById('result');
    resultDiv.style.display = 'none';

    gapi.load('client', () => {
        gapi.client.init({
            apiKey: API_KEY,
            discoveryDocs: ["https://sheets.googleapis.com/$discovery/rest?version=v4"],
        }).then(() => {
            return gapi.client.sheets.spreadsheets.values.get({
                spreadsheetId: SPREADSHEET_ID,
                range: 'Sheet1!A2:D', 
            });
        }).then(response => {
            const data = response.result.values || [];
            const orderData = data.find(row => row[0] === trackingNumber);

            let resultHtml = '';
            if (orderData) {
                const reshipTrackingNumber = orderData[1] || ''; 
                const ddlForReshipping = orderData[2] || ''; 
                const returnToSender = orderData[3] || '';

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
