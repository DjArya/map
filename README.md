# map
a map
<html>
<body>
<script src="http://d3js.org/d3.v3.min.js" charset="utf-8"></script>  
<script src="http://d3js.org/topojson.v1.min.js"></script>

<script>
//画布大小
var width = 1200;
var height = 1200;
//画布周边的空白
var padding = {left:30, right:30, top:30, bottom:30};
//在 body 里添加一个 SVG 画布	
var svg = d3.select("body")
		.append("svg")
		.attr("width", width)
		.attr("height", height);
		

			 
//颜色比例尺
var color = d3.scale.category20();

//请求geojson文件
d3.json("china.topojson", function(error, toporoot){
       if(error) console.error(error);
	   	   
	   console.log(toporoot);
	   var georoot = topojson.feature(toporoot, toporoot.objects.china);
	   
	   //定义地图投影
       var projection = d3.geo.mercator()
                          .center([107, 31])
				          .scale(600)
				          .translate([width/2, height/2]);
       //定义地理路径生成器
       var path = d3.geo.path()
                    .projection(projection)
			        .pointRadius(10);     
	   
       var groups = svg.append("g");
	   
	   var paths = groups.selectAll("path")
	         .data( georoot.features)
			 .enter()
			 .append("path")
			 .attr("class", "province")
			 .attr("fill", "#ccc")
			 .attr("stroke","black")
			 .attr("d", path);
			/* .on("click", function(d){
			 
			    var centroid = path.centroid(d);
		        var bounds = path.bounds(d);
		
		        svg.append("circle")
		           .attr("class","centroid")
		           .attr("cx", centroid[0])
		           .attr("cy", centroid[1])
		           .attr("r", 10);
			 
			 
			 });*/
			 
	   var neighbors = topojson.neighbors(toporoot.objects.china.geometries);
	   
	   paths.each(function(d,i){
	            d.neighbors = d3.selectAll(
				    neighbors[i].map(function(j){
					         return paths[0][j];
					})
				);
		    })
		    .on("mouseover",function(d,i){
			        d3.select(this).style("fill","red");
					d.neighbors.style("fill","steelblue");
			})
			.on("mouseout",function(d,i){
			        d3.select(this).style("fill","#ccc");
					d.neighbors.style("fill","#ccc");
			});
					
	    
});
</script>
</body>
</html>
