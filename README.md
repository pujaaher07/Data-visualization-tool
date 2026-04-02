<!DOCTYPE html>
<html lang="en" ng-app="dataVizApp">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>📊 Data Visualization Tool</title>
    
    <!-- AngularJS -->
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.8.2/angular.min.js"></script>
    
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@3.9.1/dist/chart.min.js"></script>
    
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            background: white;
            border-radius: 15px;
            box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
            padding: 30px;
        }

        header {
            text-align: center;
            margin-bottom: 30px;
            padding-bottom: 20px;
            border-bottom: 3px solid #667eea;
        }

        h1 {
            color: #333;
            font-size: 2.5em;
            margin-bottom: 10px;
        }

        .upload-section {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 25px;
            text-align: center;
        }

        .file-input-wrapper {
            display: inline-block;
            position: relative;
            overflow: hidden;
            margin: 10px;
        }

        .file-input-wrapper input[type=file] {
            position: absolute;
            left: -9999px;
        }

        .file-input-label {
            display: inline-block;
            padding: 12px 30px;
            background: #667eea;
            color: white;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            transition: all 0.3s ease;
        }

        .file-input-label:hover {
            background: #5568d3;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
        }

        .btn {
            padding: 12px 30px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: 600;
            font-size: 14px;
            transition: all 0.3s ease;
            margin: 5px;
        }

        .btn-reset {
            background: #dc3545;
            color: white;
        }

        .btn-reset:hover {
            background: #c82333;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(220, 53, 69, 0.4);
        }

        .alert {
            padding: 15px;
            border-radius: 8px;
            margin: 15px 0;
            text-align: center;
            font-weight: 500;
        }

        .alert-info {
            background: #d1ecf1;
            color: #0c5460;
            border: 1px solid #bee5eb;
        }

        .alert-warning {
            background: #fff3cd;
            color: #856404;
            border: 1px solid #ffeaa7;
        }

        .controls-section {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
            padding: 20px;
            background: #f8f9fa;
            border-radius: 10px;
        }

        .control-group {
            display: flex;
            flex-direction: column;
        }

        .control-group label {
            font-weight: 600;
            color: #333;
            margin-bottom: 8px;
            font-size: 14px;
        }

        .control-group select {
            padding: 10px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 14px;
            background: white;
            cursor: pointer;
            transition: border-color 0.3s ease;
        }

        .control-group select:focus {
            outline: none;
            border-color: #667eea;
        }

        .table-container {
            overflow-x: auto;
            margin-bottom: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
        }

        table {
            width: 100%;
            border-collapse: collapse;
            background: white;
        }

        th {
            background: #667eea;
            color: white;
            padding: 15px;
            text-align: left;
            font-weight: 600;
            position: sticky;
            top: 0;
        }

        td {
            padding: 12px 15px;
            border-bottom: 1px solid #eee;
        }

        tr:hover {
            background: #f8f9fa;
        }

        .chart-container {
            position: relative;
            margin: 30px auto;
            padding: 20px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
            max-width: 800px;
        }

        .chart-wrapper {
            position: relative;
            height: 400px;
        }

        .file-name {
            display: inline-block;
            margin: 10px;
            padding: 8px 15px;
            background: #e7f3ff;
            border-radius: 5px;
            color: #0066cc;
            font-weight: 500;
        }

        @media (max-width: 768px) {
            .container {
                padding: 15px;
            }

            h1 {
                font-size: 1.8em;
            }

            .controls-section {
                grid-template-columns: 1fr;
            }

            .chart-wrapper {
                height: 300px;
            }
        }
    </style>
</head>
<body ng-controller="DataVizController">
    <div class="container">
        <header>
            <h1>📊 Data Visualization Tool</h1>
            <p style="color: #666; margin-top: 10px;">Upload CSV, select columns, and visualize your data</p>
        </header>

        <!-- Upload Section -->
        <div class="upload-section">
            <div class="file-input-wrapper">
                <input type="file" 
                       id="csvFile" 
                       accept=".csv" 
                       ng-model="csvFile"
                       onchange="angular.element(this).scope().handleFileSelect(this)">
                <label for="csvFile" class="file-input-label">
                    📁 Choose CSV File
                </label>
            </div>
            <button class="btn btn-reset" ng-click="resetData()" ng-show="data.length > 0">
                🔄 Reset
            </button>
            <div ng-show="fileName" class="file-name">
                ✓ {{fileName}}
            </div>
        </div>

        <!-- No File Message -->
        <div class="alert alert-info" ng-show="data.length === 0">
            ℹ️ Please upload a CSV file to get started
        </div>

        <!-- Data Table -->
        <div ng-show="data.length > 0">
            <h2 style="margin-bottom: 15px; color: #333;">📋 Data Preview</h2>
            <div class="table-container">
                <table>
                    <thead>
                        <tr>
                            <th ng-repeat="header in headers">{{header}}</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr ng-repeat="row in data | limitTo:10">
                            <td ng-repeat="header in headers">{{row[header]}}</td>
                        </tr>
                    </tbody>
                </table>
            </div>
            <p style="text-align: center; color: #666; font-size: 14px;" ng-show="data.length > 10">
                Showing first 10 rows of {{data.length}} total rows
            </p>
        </div>

        <!-- Chart Controls -->
        <div class="controls-section" ng-show="data.length > 0">
            <div class="control-group">
                <label>📊 Chart Type</label>
                <select ng-model="chartType" ng-change="updateChart()">
                    <option value="bar">Bar Chart</option>
                    <option value="line">Line Chart</option>
                    <option value="pie">Pie Chart</option>
                </select>
            </div>

            <div class="control-group">
                <label>📍 X-Axis</label>
                <select ng-model="xAxis" ng-change="updateChart()">
                    <option value="">-- Select Column --</option>
                    <option ng-repeat="header in headers" value="{{header}}">{{header}}</option>
                </select>
            </div>

            <div class="control-group">
                <label>📈 Y-Axis</label>
                <select ng-model="yAxis" ng-change="updateChart()">
                    <option value="">-- Select Column --</option>
                    <option ng-repeat="header in headers" value="{{header}}">{{header}}</option>
                </select>
            </div>
        </div>

        <!-- Chart Display -->
        <div class="chart-container" ng-show="showChart">
            <h2 style="margin-bottom: 20px; color: #333; text-align: center;">
                📊 {{chartType | uppercase}} Chart: {{xAxis}} vs {{yAxis}}
            </h2>
            <div class="chart-wrapper">
                <canvas id="myChart"></canvas>
            </div>
        </div>
    </div>

    <script>
        var app = angular.module('dataVizApp', []);

        app.controller('DataVizController', function($scope) {
            // Initialize variables
            $scope.data = [];
            $scope.headers = [];
            $scope.fileName = '';
            $scope.chartType = 'bar';
            $scope.xAxis = '';
            $scope.yAxis = '';
            $scope.showChart = false;
            var chartInstance = null;

            // Handle file selection
            $scope.handleFileSelect = function(input) {
                var file = input.files[0];
                if (file) {
                    $scope.fileName = file.name;
                    var reader = new FileReader();
                    
                    reader.onload = function(e) {
                        var csv = e.target.result;
                        $scope.$apply(function() {
                            $scope.parseCSV(csv);
                        });
                    };
                    
                    reader.readAsText(file);
                }
            };

            // Parse CSV data
            $scope.parseCSV = function(csv) {
                var lines = csv.split('\n').filter(line => line.trim() !== '');
                
                if (lines.length === 0) {
                    alert('CSV file is empty');
                    return;
                }

                // Parse headers
                $scope.headers = lines[0].split(',').map(h => h.trim());
                
                // Parse data rows
                $scope.data = [];
                for (var i = 1; i < lines.length; i++) {
                    var values = lines[i].split(',').map(v => v.trim());
                    var row = {};
                    
                    for (var j = 0; j < $scope.headers.length; j++) {
                        row[$scope.headers[j]] = values[j];
                    }
                    
                    $scope.data.push(row);
                }

                // Auto-select first two columns
                if ($scope.headers.length >= 2) {
                    $scope.xAxis = $scope.headers[0];
                    $scope.yAxis = $scope.headers[1];
                    $scope.updateChart();
                }
            };

            // Update chart
            $scope.updateChart = function() {
                if (!$scope.xAxis || !$scope.yAxis) {
                    $scope.showChart = false;
                    return;
                }

                $scope.showChart = true;

                // Prepare data for chart
                var labels = [];
                var values = [];

                $scope.data.forEach(function(row) {
                    labels.push(row[$scope.xAxis]);
                    var value = parseFloat(row[$scope.yAxis]);
                    values.push(isNaN(value) ? 0 : value);
                });

                // Limit data points for better visualization
                if (labels.length > 20) {
                    labels = labels.slice(0, 20);
                    values = values.slice(0, 20);
                }

                // Destroy previous chart instance
                if (chartInstance) {
                    chartInstance.destroy();
                }

                // Create new chart
                setTimeout(function() {
                    var ctx = document.getElementById('myChart').getContext('2d');
                    
                    var chartConfig = {
                        type: $scope.chartType,
                        data: {
                            labels: labels,
                            datasets: [{
                                label: $scope.yAxis,
                                data: values,
                                backgroundColor: [
                                    'rgba(102, 126, 234, 0.7)',
                                    'rgba(118, 75, 162, 0.7)',
                                    'rgba(237, 100, 166, 0.7)',
                                    'rgba(255, 159, 64, 0.7)',
                                    'rgba(75, 192, 192, 0.7)',
                                    'rgba(153, 102, 255, 0.7)',
                                    'rgba(255, 99, 132, 0.7)',
                                    'rgba(54, 162, 235, 0.7)',
                                    'rgba(255, 206, 86, 0.7)',
                                    'rgba(231, 233, 237, 0.7)'
                                ],
                                borderColor: [
                                    'rgba(102, 126, 234, 1)',
                                    'rgba(118, 75, 162, 1)',
                                    'rgba(237, 100, 166, 1)',
                                    'rgba(255, 159, 64, 1)',
                                    'rgba(75, 192, 192, 1)',
                                    'rgba(153, 102, 255, 1)',
                                    'rgba(255, 99, 132, 1)',
                                    'rgba(54, 162, 235, 1)',
                                    'rgba(255, 206, 86, 1)',
                                    'rgba(231, 233, 237, 1)'
                                ],
                                borderWidth: 2
                            }]
                        },
                        options: {
                            responsive: true,
                            maintainAspectRatio: false,
                            plugins: {
                                legend: {
                                    display: $scope.chartType === 'pie',
                                    position: 'bottom'
                                },
                                title: {
                                    display: false
                                }
                            },
                            scales: $scope.chartType !== 'pie' ? {
                                y: {
                                    beginAtZero: true,
                                    ticks: {
                                        font: {
                                            size: 12
                                        }
                                    }
                                },
                                x: {
                                    ticks: {
                                        font: {
                                            size: 12
                                        },
                                        maxRotation: 45,
                                        minRotation: 45
                                    }
                                }
                            } : {}
                        }
                    };

                    chartInstance = new Chart(ctx, chartConfig);
                }, 100);
            };

            // Reset all data
            $scope.resetData = function() {
                $scope.data = [];
                $scope.headers = [];
                $scope.fileName = '';
                $scope.xAxis = '';
                $scope.yAxis = '';
                $scope.showChart = false;
                
                if (chartInstance) {
                    chartInstance.destroy();
                    chartInstance = null;
                }
                
                document.getElementById('csvFile').value = '';
            };
        });
    </script>
</body>
</html>
