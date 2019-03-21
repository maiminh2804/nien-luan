<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>DHT11</title>
    <!-- Nhúng file Javasript tại đường dẫn src để có thể xây dựng 1 graph -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
    <script type="text/javascript" src="https://canvasjs.com/assets/script/canvasjs.min.js"></script>
    <script type="text/javascript" src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script  type="text/javascript" src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script> 
</head>
<body background ="https://visme.co/blog/wp-content/uploads/2017/07/50-Beautiful-and-Minimalist-Presentation-Backgrounds-036.jpg?fbclid=IwAR1ICbX1XjRwcHxQfz274jNYf4VDXJ4NUUdKTMIWXzOS1RI8jeJDNKeqKms">
        <h1 align="center">TEMPERATURE, HUMIDITY MONITORING <br> AND FLAME DETECTION</h1>
        <h2>1. PARAMETER </h2><br> 
        <h3><font color=#F08080> Temprature</font></h3> <input type="text" size="6" id="temp">&#176;C<br>
        <h3><font color=#5aede3> Humidity</font></h3> <input type="text"  size="6" id="humd">%<br><br>

        <h2> 2. GRAPH</h2><br>
        <!-- thiết lập kích thước cho graph thông qua id ChartContainer đã thiết lập ở trên  -->
        <div id="ChartContainer" style="height: 300px; width:80%;"></div>
        <script type="text/javascript">
            function httpGetAsync(theUrl, callback) { 
                        var xmlHttp = new XMLHttpRequest();
                        xmlHttp.onreadystatechange = function() {
                    if (xmlHttp.readyState == 4 && xmlHttp.status == 200)
                        callback(JSON.parse(xmlHttp.responseText));
                }
                xmlHttp.open("GET", theUrl, true); // true for asynchronous
                xmlHttp.send(null);
            }

            window.onload = function() {
                var dataTemp = [];
                var dataHumd = [];
                var dataFlame = [];

                var Chart = new CanvasJS.Chart("ChartContainer", {
                    zoomEnabled: true, // Dùng thuộc tính có thể zoom vào graph
                    theme:"light2",
                    title: {
                        text: "Temprature and Humidity" // Viết tiêu đề cho graph
                    },
                    toolTip: { // Hiển thị cùng lúc 2 trường giá trị nhiệt độ, độ ẩm trên graph
                        shared: true
                    },
                    axisX: {
                        title: "Chart updates every 10 secs", // Chú thích cho trục X
                        crosshair: {
			             enabled: true,
			             snapToDataPoint: true
		                }
                    },
                    axisY: {
                        title: " ", // Chú thích cho trục Y
                        crosshair: {
			             enabled: true
		                }
                    },
                    data: [{
                            // Khai báo các thuộc tính của dataTemp và dataHumd
                            type: "line", // Chọn kiểu dữ liệu đường
                            xValueType: "dateTime", // Cài đặt kiểu giá trị tại trục X là thuộc tính thời gian
                            showInLegend: true, // Hiển thị "temp" ở mục chú thích (legend items)
                            name: "temp",
                            markerType: "square",
                            color: "#F08080",   
                            dataPoints: dataTemp // Dữ liệu hiển thị sẽ lấy từ dataTemp
                        },
                        {
                            type: "line",
                            xValueType: "dateTime",
                            showInLegend: true,
                            lineDashType: "dash",
                            name: "humd",
                            dataPoints: dataHumd
                        }
                        ],
                    });

                    
                var yHumdVal = 0; // Biến lưu giá trị độ ẩm (theo trục Y)
                var yTempVal = 0; // Biến lưu giá trị nhiệt độ (theo trục Y)
                var yFlameval = 0;
                var updateInterval = 5000;  // Thời gian cập nhật dữ liệu 10000ms = 10s
                var time = new Date(); // Lấy thời gian hiện tại
                var updateInterval1= 1000; // Thời gian cập nhật dữ liệu 2000ms = 2s
                    

                var updateChart = function() {
                    httpGetAsync('/get', function(data) {

                        // Gán giá trị từ localhost:8000/get vào textbox để hiển thị
                        document.getElementById("temp").value = data[0].temp;
                        document.getElementById("humd").value = data[0].humd;

                        
                        // Xuất ra màn hình console trên browser giá trị nhận được từ localhost:8000/get
                        console.log(data);
                        // Cập nhật thời gian và lấy giá trị nhiệt độ, độ ẩm từ server
                        time.setTime(time.getTime() + updateInterval);
                        yTempVal = parseInt(data[0].temp);
                        yHumdVal = parseInt(data[0].humd);
                        dataTemp.push({ // cập nhât dữ liệu mới từ server
                            x: time.getTime(),
                            y: yTempVal
                        });
                        dataHumd.push({
                            x: time.getTime(),
                            y: yHumdVal
                        });
                        
                        Chart.render(); // chuyển đổi dữ liệu của của graph thành mô hình đồ họa
                                                
                        // setInterval(function() { 
                        //     updateFlame()
                        // }, updateInterval1);  
                    });
                };
                
                var updateFlame = function(){
                    httpGetAsync('/get', function(data) {
                        if (data[0].flame == 27) 
                           document.getElementById("flame").value = "FIRE!!!!";
                        else
                        document.getElementById("flame").value = "Normal";
                        
                        time.setTime(time.getTime() + updateInterval1);
                        //  yFlameval = data[0].flame;
                        // dataFlame.push({ // cập nhât dữ liệu mới từ server
                        //     x: time.getTime(),
                        //     y: yFlameval
                       // });
                    });
                };

                updateChart(); // Chạy lần đầu tiên
                updateFlame();
                
                setInterval(function() { // Cập nhật lại giá trị graph sau thời gian updateInterval
                    updateChart()
                }, updateInterval);

                setInterval(function() { 
                    updateFlame()
                }, updateInterval1);  
                              
            }            
        </script>
        <br>
        <br> 
        <h2>3. FLAME</h2> <input type="text" size="6" id="flame"><br><br>      
</body>
</html>
