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
            display: flex;
        }

        /* Sidebar styles */
        .sidebar {
            width: 250px;
            background-color: #2c3e50;
            color: #ecf0f1;
            padding: 20px;
            height: 100vh;
            position: fixed;
            left: 0;
            top: 0;
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            transition: all 0.3s ease;
            transform: translateX(-100%);
        }

        .sidebar.open {
            transform: translateX(0);
        }

        .sidebar h2 {
            text-align: center;
            color: #ecdbdb;
        }

        .sidebar a {
            text-decoration: none;
            color: #ecf0f1;
            padding: 10px 0;
            display: block;
            font-size: 18px;
        }

        .sidebar a:hover {
            background-color: #3498db;
            padding-left: 10px;
            transition: padding-left 0.2s;
        }

        .toggle-btn {
            font-size: 24px;
            cursor: pointer;
            background-color: #3498db;
            padding: 10px 20px;
            color: #fff;
            border: none;
            position: fixed;
            left: 10px;
            top: 10px;
            border-radius: 5px;
        }

        main {
            margin-left: 0;
            margin-right: auto;
            padding: 20px;
            width: 100%;
            transition: margin-left 0.3s ease;
        }

        main.sidebar-open {
            margin-left: 270px;
        }

        h1, h2 {
            text-align: center;
            color: #2c3e50;
        }

        label {
            display: block;
            margin-bottom: 10px;
            font-weight: bold;
        }

        input[type="text"] {
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

        hr {
            border: 0;
            border-top: 1px solid #eee;
            margin: 40px 0;
        }

        #result {
            margin-top: 20px;
        }

        #result p {
            font-size: 16px;
        }
    </style>
</head>
<body>
    <!-- Sidebar -->
    <div class="sidebar" id="sidebar">
        <h2>Sustainability</h2>
        <nav>
            <a href="#adminSection">Admin Panel</a>
            <a href="#categoryPanel">Manage Categories</a>
            <a href="#fileUploadSection">File Upload and History</a>
            <a href="#dataSection">All Order Data</a>
        </nav>
    </div>

    <!-- Main content -->
    <main id="mainContent">
        <button class="toggle-btn" onclick="toggleSidebar()">☰ Sustainability</button>
        <h1>Order Management System</h1>

        <!-- Section for searching orders -->
        <section id="searchSection">
            <h2>Search Your Order</h2>
            <label for="trackingNumber">Enter Tracking Number or Order ID:</label>
            <input type="text" id="trackingNumber">
            <button onclick="searchOrder()">Search</button>
            <div id="result" style="display:none;"></div>
        </section>
    </main>

    <script>
        const sidebar = document.getElementById('sidebar');
        const mainContent = document.getElementById('mainContent');

        function toggleSidebar() {
            sidebar.classList.toggle('open');
            mainContent.classList.toggle('sidebar-open');
        }
    </script>
</body>
</html>
