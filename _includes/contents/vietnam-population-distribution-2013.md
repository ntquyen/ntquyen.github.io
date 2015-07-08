<script type="text/javascript">

// jsData
function gvisDataVN_population () {
var data = new google.visualization.DataTable();
var datajson =
[
  [ "VN-44", "An Giang", 2155.3 ],
  [ "VN-43","Bà Rịa Vũng Tàu",1052.8 ],
  [ "VN-54","Bắc Giang",1593.2 ],
  [ "VN-53","Bắc Kạn",303.1 ],
  [ "VN-55","Bạc Liêu",876.8 ],
  [ "VN-56","Bắc Ninh",1114 ],
  [ "VN-50","Bến Tre",1262 ],
  [ "VN-31","Bình Định",1510.4 ],
  [ "VN-57","Bình Dương",1802.5 ],
  [ "VN-58","Bình Phước",921.8 ],
  [ "VN-40","Bình Thuận",1201.2 ],
  [ "VN-59","Cà Mau",1219.9 ],
  [ "VN-CT","Cần Thơ",1222.4 ],
  [ "VN-04","Cao Bằng",517.9 ],
  [ "VN-DN","Đà Nẵng",992.8 ],
  [ "VN-33","Đắk Lắk",1827.8 ],
  [ "VN-72","Đắk Nông",553.2 ],
  [ "VN-71","Điện Biên",527.3 ],
  [ "VN-39","Đồng Nai",2768.7 ],
  [ "VN-45","Đồng Tháp",1680.3 ],
  [ "VN-30","Gia Lai",1359.9 ],
  [ "VN-03","Hà Giang",771.2 ],
  [ "VN-63","Hà Nam",794.3 ],
  [ "VN-HN","Hà Nội",6936.9 ],
  [ "VN-23","Hà Tĩnh",1242.7 ],
  [ "VN-61","Hải Dương",1747.5 ],
  [ "VN-HP","Hải Phòng",1925.2 ],
  [ "VN-73","Hậu Giang",773.8 ],
  [ "VN-SG","Hồ Chí Minh",7818.2 ],
  [ "VN-66","Hưng Yên",1151.6 ],
  [ "VN-47","Kiên Giang",1738.8 ],
  [ "VN-28","Kon Tum",473.3 ],
  [ "VN-01","Lai Châu",404.5 ],
  [ "VN-35","Lâm Đồng",1246.2 ],
  [ "VN-09","Lạng Sơn",751.2 ],
  [ "VN-02","Lào Cai",656.9 ],
  [ "VN-41","Long An",1469.9 ],
  [ "VN-67","Nam Định",1839.9 ],
  [ "VN-22","Nghệ An",2978.7 ],
  [ "VN-18","Ninh Bình",927 ],
  [ "VN-36","Ninh Thuận",587.4 ],
  [ "VN-68","Phú Thọ",1351 ],
  [ "VN-32","Phú Yên",883.2 ],
  [ "VN-24","Quảng Bình",863.4 ],
  [ "VN-27","Quảng Nam",1461 ],
  [ "VN-29","Quảng Ngãi",1236.3 ],
  [ "VN-13","Quảng Ninh",1185.2 ],
  [ "VN-25","Quảng Trị",612.5 ],
  [ "VN-52","Sóc Trăng",1308.3 ],
  [ "VN-05","Sơn La",1149.3 ],
  [ "VN-37","Tây Ninh",1095.6 ],
  [ "VN-20","Thái Bình",1788.4 ],
  [ "VN-69","Thái Nguyên",1156 ],
  [ "VN-21","Thanh Hóa",3476.6 ],
  [ "VN-26","Thừa Thiên Huế",1123.8 ],
  [ "VN-46","Tiền Giang",1703.4 ],
  [ "VN-51","Trà Vinh",1027.5 ],
  [ "VN-07","Tuyên Quang",746.7 ],
  [ "VN-49","Vĩnh Long",1040.5 ],
  [ "VN-70","Vĩnh Phúc",1029.4 ],
  [ "VN-06","Yên Bái",771.6  ]
];

data.addColumn('string','code');
data.addColumn('string','province');
data.addColumn('number','population');
data.addRows(datajson);
  return(data);
}

function drawChartVN_population() {
  var data = gvisDataVN_population();
  var options = {};
  options["width"] = 800;
  options["height"] = 550;
  options["dataMode"] = "regions";
  options["resolution"] = "provinces";
  options["region"] = "VN";
  options["backgroundColor"] = "lightblue";
  options["colorAxis"] = {colors: ['red']};

  var chart = new google.visualization.GeoChart(
    document.getElementById('VN_population')
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
    callbacks.push(drawChartVN_population);
  }
)();

function displayChartVN_population() {
  var pkgs = window.__gvisPackages = window.__gvisPackages || [];
  var callbacks = window.__gvisCallbacks = window.__gvisCallbacks || [];

  window.clearTimeout(window.__gvisLoad);
  // The timeout is set to 100 because otherwise the container div we are
  // targeting might not be part of the document yet
  //
  window.__gvisLoad = setTimeout(function() {
    var pkgCount = pkgs.length;

    google.load("visualization", "1",
      { packages:pkgs, callback: function() {
        if (pkgCount != pkgs.length) {
          // Race condition where another setTimeout call snuck in after us; if
          // that call added a package, we must not shift its callback
          return;
        }
        while (callbacks.length > 0)
          callbacks.shift()();
        }
      });

  }, 100);
}

</script>


<!-- jsChart -->
<script type="text/javascript"
src="https://www.google.com/jsapi?callback=displayChartVN_population"></script>

<!-- divChart -->

<div id="VN_population"
  style="width: 800; height: 550;">
</div>
