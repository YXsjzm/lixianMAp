# 离线地图
支持离线（内网）使用百度地图进行数据可视化展示

## 1、配置
在 `apiv2.0.1021.min.js` 中设置瓦片数据服务器地址：
```
// miningmap、maptile的地址配置，部署后修改此处配置即可
window.ARGS = {
	url:"k1268.mlamp.co/miningmap",
	miningmap : "http://k1268.mlamp.co/miningmap",
	maptile : "http://k1268.mlamp.co/maptile"
};
```

## 2、调用示例
```
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
    <meta name="viewport" content="initial-scale=1.0, user-scalable=no" />
    <title>离线地图演示</title>
    <!-- 引入离线地图css/js资源 -->
    <link rel="stylesheet" type="text/css" href="css/bmap.css"/>
    <link rel="stylesheet" type="text/css" href="css/DrawingManager_min.css"/>
    <script type="text/javascript" src="js/apiv2.0.1021.min.js"></script>
    <script type="text/javascript" src="js/DrawingManager_min.js"></script>
</head>
<body>
	<div id="container" style="width:100%;min-height:500px;border:1px solid gray;"></div>
</body>
</html>
<script type="text/javascript">
    var map = new BMap.Map("container");          // 创建地图实例

    var point = new BMap.Point(115.077, 38.973); // 创建点坐标
    map.centerAndZoom(point, 7); // 初始化地图,设置中心点坐标和地图级别。
    map.addControl(new BMap.NavigationControl());
    map.enableScrollWheelZoom(); // 启用滚轮放大缩小。
    map.enableKeyboard(); // 启用键盘操作。  
    map.setCurrentCity("北京"); // 设置地图显示的城市 此项是必须设置的
    
    
    /* 工具栏使用 */
    var overlays = {};
    var overlaycomplete = function(e) {
    	var point = new BMap.Point(e.overlay.point.lng, e.overlay.point.lat);
    	var radius = e.overlay.ma;
    
    	console.log('圆点：' + JSON.stringify(point));
    	console.log('半径：' + radius + '米');
    
    	if(radius < 1000) {
    		return;
    	}
    
    	//if(circle) {
    	//	map.removeOverlay(circle);
    	//}
    	for(i = 0; i < overlays.length; i++) {
    		map.removeOverlay(overlays[i]);
    	}
    	overlays = [];
    	overlays.push(e.overlay);
    
    	//	map.clearOverlays();
    	//	map.addOverlay(e.overlay);
    
    	// circle = e.overlay;
    
    	// 测试：模拟10个随机的点
    	for(var i = 0; i < 10; i++) {
    		var lng = point.lng + (Math.random() - 0.5) * 2;
    		var lat = point.lat + (Math.random() - 0.5) * 2;
    		var p = new BMap.Point(lng, lat);
    		var mk = new BMap.Marker(p);
    		map.addOverlay(mk);
    		overlays.push(mk);
    	}
    
    	// 正式：调用接口，获取圆内的经纬度点，并标注
    	/*$.ajax({
    		url: '',
    		data: {
    			lng: point.lng,
    			lat: point.lat,
    			radius: radius
    		},
    		success: function(result) {
    			if(result && result.statusCode == '200') {
    				$(result.listObj).each(function(i,obj){
    					var p = new BMap.Point(obj.lng, obj.lat);
    					var mk = new BMap.Marker(p);
    					map.addOverlay(mk);
    					overlays.push(mk);
    				});
    			}
    		}
    	});*/
    };
    var styleOptions = {
    	strokeColor: "#a00", //边线颜色。
    	fillColor: "#aaa", //填充颜色。当参数为空时，圆形将没有填充效果。
    	strokeWeight: 2, //边线的宽度，以像素为单位。
    	strokeOpacity: 0.75, //边线透明度，取值范围0 - 1。
    	fillOpacity: 0.5, //填充的透明度，取值范围0 - 1。
    	strokeStyle: 'solid' //边线的样式，solid或dashed。
    }
    //实例化鼠标绘制工具
    var drawingManager = new BMapLib.DrawingManager(map, {
    	isOpen: false, //是否开启绘制模式
    	enableDrawingTool: true, //是否显示工具栏
    	drawingToolOptions: {
    		anchor: BMAP_ANCHOR_TOP_RIGHT, //位置
    		offset: new BMap.Size(5, 5), //偏离值
    	},
    	circleOptions: styleOptions //, //圆的样式
    	//polylineOptions: styleOptions, //线的样式
    	//polygonOptions: styleOptions, //多边形的样式
    	//rectangleOptions: styleOptions //矩形的样式
    });
    //添加鼠标绘制工具监听事件，用于获取绘制结果
    drawingManager.addEventListener('overlaycomplete', overlaycomplete);
</script>
```