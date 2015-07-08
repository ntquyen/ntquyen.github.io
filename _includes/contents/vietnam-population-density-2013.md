<!-- GeoChart generated in R 3.1.2 by googleVis 0.5.8 package -->
<!-- Thu Jun 18 23:26:47 2015 -->


<!-- jsHeader -->
<script type="text/javascript">


// jsData
function gvisDataVN_density () {
var data = new google.visualization.DataTable();
var datajson = [
  [ "VN-44", "An Giang", 609 ],
  [ "VN-43", "Bà Rịa Vũng Tàu", 529 ],
  [ "VN-54", "Bắc Giang", 414 ],
  [ "VN-53", "Bắc Kạn", 62 ],
  [ "VN-55", "Bạc Liêu", 355 ],
  [ "VN-56", "Bắc Ninh", 1354 ],
  [ "VN-50", "Bến Tre", 535 ],
  [ "VN-31", "Bình Định", 250 ],
  [ "VN-57", "Bình Dương", 669 ],
  [ "VN-58", "Bình Phước", 134 ],
  [ "VN-40", "Bình Thuận", 154 ],
  [ "VN-59", "Cà Mau", 230 ],
  [ "VN-CT", "Cần Thơ", 868 ],
  [ "VN-04", "Cao Bằng", 77 ],
  [ "VN-DN", "Đà Nẵng", 772 ],
  [ "VN-33", "Đắk Lắk", 139 ],
  [ "VN-72", "Đắk Nông", 85 ],
  [ "VN-71", "Điện Biên", 55 ],
  [ "VN-39", "Đồng Nai", 469 ],
  [ "VN-45", "Đồng Tháp", 497 ],
  [ "VN-30", "Gia Lai", 88 ],
  [ "VN-03", "Hà Giang", 97 ],
  [ "VN-63", "Hà Nam", 923 ],
  [ "VN-HN", "Hà Nội", 2087 ],
  [ "VN-23", "Hà Tĩnh", 207 ],
  [ "VN-61", "Hải Dương", 1055 ],
  [ "VN-HP", "Hải Phòng", 1260 ],
  [ "VN-73", "Hậu Giang", 483 ],
  [ "VN-SG", "Hồ Chí Minh", 3731 ],
  [ "VN-66", "Hưng Yên", 1244 ],
  [ "VN-47", "Kiên Giang", 274 ],
  [ "VN-28", "Kon Tum", 49 ],
  [ "VN-01", "Lai Châu", 45 ],
  [ "VN-35", "Lâm Đồng", 128 ],
  [ "VN-09", "Lạng Sơn", 90 ],
  [ "VN-02", "Lào Cai", 103 ],
  [ "VN-41", "Long An", 327 ],
  [ "VN-67", "Nam Định", 1113 ],
  [ "VN-22", "Nghệ An", 181 ],
  [ "VN-18", "Ninh Bình", 673 ],
  [ "VN-36", "Ninh Thuận", 175 ],
  [ "VN-68", "Phú Thọ", 382 ],
  [ "VN-32", "Phú Yên", 175 ],
  [ "VN-24", "Quảng Bình", 107 ],
  [ "VN-27", "Quảng Nam", 140 ],
  [ "VN-29", "Quảng Ngãi", 240 ],
  [ "VN-13", "Quảng Ninh", 194 ],
  [ "VN-25", "Quảng Trị", 129 ],
  [ "VN-52", "Sóc Trăng", 395 ],
  [ "VN-05", "Sơn La", 81 ],
  [ "VN-37", "Tây Ninh", 272 ],
  [ "VN-20", "Thái Bình", 1139 ],
  [ "VN-69", "Thái Nguyên", 327 ],
  [ "VN-21", "Thanh Hóa", 312 ],
  [ "VN-26", "Thừa Thiên Huế", 223 ],
  [ "VN-46", "Tiền Giang", 679 ],
  [ "VN-51", "Trà Vinh", 439 ],
  [ "VN-07", "Tuyên Quang", 127 ],
  [ "VN-49", "Vĩnh Long", 684 ],
  [ "VN-70", "Vĩnh Phúc", 831 ],
  [ "VN-06", "Yên Bái", 112 ]
];
data.addColumn('string','code');
data.addColumn('string','province');
data.addColumn('number','density');
data.addRows(datajson);
return(data);
}


// jsDrawChart
function drawChartVN_density() {
  var data = gvisDataVN_density();
  var options = {};
  options["width"] =    800;
  options["height"] =    550;
  options["dataMode"] = "regions";
  options["resolution"] = "provinces";
  options["region"] = "VN";
  options["backgroundColor"] = "lightblue";
  options["colorAxis"] = {colors: ['red']};


  var chart = new google.visualization.GeoChart(
    document.getElementById('VN_density')
  );
  chart.draw(data,options);
}



// jsDisplayChart
(
  function() {
  var pkgs = window.__gvisPackages = window.__gvisPackages || [];
  var callbacks = window.__gvisCallbacks = window.__gvisCallbacks || [];
  var chartid = "geochart";

  // Manually see if chartid is in pkgs (not all browsers support Array.indexOf)
  var i, newPackage = true;
  for (i = 0; newPackage && i < pkgs.length; i++) {
  if (pkgs[i] === chartid)
  newPackage = false;
  }
  if (newPackage)
    pkgs.push(chartid);

  // Add the drawChart function to the global list of callbacks
  callbacks.push(drawChartVN_density);
  })();
  function displayChartVN_density() {
    var pkgs = window.__gvisPackages = window.__gvisPackages || [];
    var callbacks = window.__gvisCallbacks = window.__gvisCallbacks || [];
    window.clearTimeout(window.__gvisLoad);
    // The timeout is set to 100 because otherwise the container div we are
    // targeting might not be part of the document yet
    window.__gvisLoad = setTimeout(function() {
    var pkgCount = pkgs.length;
    google.load("visualization", "1", { packages:pkgs, callback: function() {
    if (pkgCount != pkgs.length) {
    // Race condition where another setTimeout call snuck in after us; if
    // that call added a package, we must not shift its callback
    return;
  }
  while (callbacks.length > 0)
  callbacks.shift()();
  } });
  }, 100);
}


// jsFooter
</script>


<!-- jsChart -->
<script type="text/javascript" src="https://www.google.com/jsapi?callback=displayChartVN_density"></script>


<!-- divChart -->

<div id="VN_density"
  style="width: 800; height: 550;">
</div>
