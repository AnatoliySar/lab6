<html>
<body>
<canvas id='lab6' height='1000' width='1000'>
<script type='text/javascript'>
	var canvas = document.getElementById('lab6');
	var ctx = canvas.getContext('2d');
	var points = new Array();
	function blockMenu() {	
    		document.getElementById("lab6").innerHTML = "Íàæàòà ïðàâàÿ êíîïêà ìûøè";
	}
	document.oncontextmenu = function()  { blockMenu(); return false; };
	function compareNumeric(a, b) {
  		if (a > b) return 1;
  		if (a < b) return -1;
	}
	function vectorproduct(vector1, vector2) {
		var result = new Array();
		result[0] = vector1[1]*vector2[2] - vector2[1]*vector1[2];
		result[1] = (-1)*(vector1[0]*vector2[2] - vector2[0]*vector1[2]);
		result[2] = vector1[0]*vector2[1] - vector2[0]*vector1[1];
		return result;
	}
	function getZ(points, point) {
		var vector1 = new Array(points[1][0]-points[0][0], points[1][1]-points[0][1], points[1][2] - points[0][2]);
		var vector2 = new Array(points[2][0]-points[0][0], points[2][1]-points[0][1], points[2][2] - points[0][2]);
		var normal = vectorproduct(vector1, vector2);
		var z = (-1)*( normal[0]*( point[0] - points[0][0] ) + normal[1]*( point[1] - points[0][1] ) - normal[2]*points[0][2])/normal[2];
		return z;
	}
	function draw(points, ymin, ymax, zbuf) {
		var arr = new Array();
		for (var i = ymin; i <= ymax; ++i)
			arr[i] = new Array();
		var ytemp, xtemp, proizv, buf, deltay1, deltay2;
		for (var i = 0; i < points.length-1; ++i) {
			if (i != 0) {
				deltay2 = deltay1;
				deltay1 = points[i+1][1] - points[i][1];
			}
			else {
				deltay1 = points[i+1][1] - points[i][1];
				deltay2 = deltay1;
			}
			proizv = (points[i+1][0] - points[i][0])/(Math.abs(points[i+1][1] - points[i][1]));
			xtemp = points[i][0];
			if (points[i+1][1] > points[i][1]) {
				if (deltay1*deltay2 > 0) {
					for (var j = points[i][1]; j < points[i+1][1]; ++j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
				else {
					for (var j = points[i][1]+1; j < points[i+1][1]; ++j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
			}
			else if (points[i+1][1] < points[i][1]) {
				if (deltay1*deltay2 > 0) {
					for (var j = points[i][1]; j > points[i+1][1]; --j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
				else {
					for (var j = points[i][1]-1; j > points[i+1][1]; --j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
			}
			else {
				ytemp = points[i][1];
				buf = arr[ytemp].length;
				arr[ytemp][buf] = points[i][0];
				arr[ytemp][buf+1] = points[i+1][0];
			}
		}
		deltay2 = deltay1;
		deltay1 = points[0][1] - points[points.length-1][1];		
		proizv = (points[0][0] - points[points.length-1][0])/(Math.abs(points[0][1] - points[points.length-1][1]));
		xtemp = points[points.length-1][0];
			if (points[0][1] > points[points.length-1][1]) {
				if (deltay1*deltay2 > 0) {
					for (var j = points[points.length-1][1]; j < points[0][1]; ++j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
				else {
					for (var j = points[points.length-1][1]+1; j < points[0][1]; ++j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
			}
			else if (points[0][1] < points[points.length-1][1]) {
				if (deltay1*deltay2 > 0) {
					for (var j = points[points.length-1][1]; j > points[0][1]; --j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
				else {
					for (var j = points[points.length-1][1]-1; j > points[0][1]; --j) {
						buf = arr[j].length;
						arr[j][buf] = xtemp;
						xtemp += proizv;
					}
				}
			}
			else {
				ytemp = points[points.length-1][1];
				buf = arr[ytemp].length;
				arr[ytemp][buf] = points[points.length-1][0];
				arr[ytemp][buf+1] = points[0][0];
			}
		
		for (var i = ymin; i <= ymax; ++i)
			arr[i].sort(compareNumeric);
		for (var i = ymin; i <= ymax; ++i) {
			for (var j = 0; j < arr[i].length; j += 2) {
				for (var u = arr[i][j]; u < arr[i][j+1]; u++) {
					if ( getZ(points, new Array(u,i)) > zbuf[Math.round(u)][i]) {
						ctx.fillRect(u, i, 2, 2);
						zbuf[Math.round(u)][i] = getZ(points, new Array(u,i));
					}
				}
			}
		}
		return zbuf;		
	}
		var zbuf = new Array();
		for (var i = 0; i < 1000; ++i) {
			zbuf[i] = new Array();
			for (var j = 0; j < 1000; ++j) {
				zbuf[i][j] = -100;
			}
		}
		ctx.fillStyle="#00008b";
		points[0] = new Array(500, 200, 20);
		points[1] = new Array(500, 600, 20);
		points[2] = new Array(100, 400, 100);
		zbuf = draw(points, 200, 600, zbuf);
		points = [];
		
		ctx.fillStyle="#f80000";
		points[0] = new Array(200, 250, 20);
		points[1] = new Array(200, 550, 20);
		points[2] = new Array(400, 400, 100);
		zbuf = draw(points, 200, 600, zbuf);		
</script>
</canvas>
</body>
</html>
