<!DOCTYPE html>
<html>
<head>
    <title>Event Security Pay Sheet</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f8f8f8;
            text-align: center;
            color: #000;
        }
        #logo {
            width: 300px;
            margin: 20px auto;
        }
        table {
            margin: auto;
            width: 80%;
            border-collapse: collapse;
            background-color: white;
        }
        th, td {
            border: 1px solid black;
            padding: 8px;
            text-align: center;
        }
        th {
            background-color: #003366;
            color: white;
        }
        button {
            background-color: #cc0000;
            color: white;
            padding: 10px 15px;
            margin: 10px;
            border: none;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #990000;
        }
    </style>
    <script>
        function saveData() {
            let formData = {};
            document.querySelectorAll('input').forEach(input => {
                formData[input.id] = input.value;
            });
            localStorage.setItem('securityPaySheet', JSON.stringify(formData));
            alert('Data saved successfully!');
        }

        function loadData() {
            let savedData = localStorage.getItem('securityPaySheet');
            if (savedData) {
                savedData = JSON.parse(savedData);
                document.querySelectorAll('input').forEach(input => {
                    input.value = savedData[input.id] || '';
                });
            }
        }

        function resetData() {
            localStorage.removeItem('securityPaySheet');
            document.querySelectorAll('input').forEach(input => input.value = '');
            alert('Data reset successfully!');
        }

        function calculateTotalHours(index) {
            let timeIn = document.getElementById(`timein${index}`).value;
            let timeOut = document.getElementById(`timeout${index}`).value;
            
            if (timeIn && timeOut) {
                let [inHours, inMinutes] = timeIn.split(':').map(Number);
                let [outHours, outMinutes] = timeOut.split(':').map(Number);
                
                let start = inHours * 60 + inMinutes;
                let end = outHours * 60 + outMinutes;
                
                if (end < start) {
                    end += 1440; // Handle overnight shifts
                }
                
                let totalMinutes = end - start;
                let totalHours = (totalMinutes / 60).toFixed(2);
                document.getElementById(`totalhours${index}`).value = totalHours;
            }
        }

        function submitForm() {
            let data = 'EVENT SECURITY PAY SHEET\n\n';
            data += 'Event: ' + document.getElementById('event').value + '\n';
            data += 'Date: ' + document.getElementById('date').value + '\n\n';
            data += 'Last Name, First Name, Time-In, Time-Out, Total Hours, Radio #, Notes\n';
            
            for (let i = 1; i <= 10; i++) {
                data += document.getElementById('lastname' + i).value + ', ';
                data += document.getElementById('firstname' + i).value + ', ';
                data += document.getElementById('timein' + i).value + ', ';
                data += document.getElementById('timeout' + i).value + ', ';
                data += document.getElementById('totalhours' + i).value + ', ';
                data += document.getElementById('radio' + i).value + ', ';
                data += document.getElementById('notes' + i).value + '\n';
            }

            window.location.href = 'mailto:hlee@lvms.com?subject=Event Security Pay Sheet&body=' + encodeURIComponent(data);
        }
    </script>
</head>
<body onload="loadData()">
    <img id="logo" src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSjlcsupsGBKup4buW90-q4I2cRwAfrhebYRw&s" alt="Las Vegas Motor Speedway Logo">
    <h2>Event Security Pay Sheet</h2>
    <label>Event:</label> <input type="text" id="event"><br>
    <label>Date:</label> <input type="text" id="date"><br><br>
    
    <table>
        <tr>
            <th>Last Name</th>
            <th>First Name</th>
            <th>Time-In (HH:MM)</th>
            <th>Time-Out (HH:MM)</th>
            <th>Total Hours</th>
            <th>Radio #</th>
            <th>Notes</th>
        </tr>
        <script>
            for (let i = 1; i <= 10; i++) {
                document.write('<tr>');
                document.write(`<td><input type='text' id='lastname${i}'></td>`);
                document.write(`<td><input type='text' id='firstname${i}'></td>`);
                document.write(`<td><input type='time' id='timein${i}' oninput='calculateTotalHours(${i})'></td>`);
                document.write(`<td><input type='time' id='timeout${i}' oninput='calculateTotalHours(${i})'></td>`);
                document.write(`<td><input type='text' id='totalhours${i}' readonly></td>`);
                document.write(`<td><input type='text' id='radio${i}'></td>`);
                document.write(`<td><input type='text' id='notes${i}'></td>`);
                document.write('</tr>');
            }
        </script>
    </table><br>
    
    <button onclick="saveData()">Save</button>
    <button onclick="resetData()">Reset Data</button>
    <button onclick="submitForm()">Submit</button>
</body>
</html>
