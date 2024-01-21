<script>
  // @ts-nocheck

  import { browser } from "$app/environment";

  const auth_link = "https://www.strava.com/api/v3/oauth/token";
  const streak_start_date = "2024-01-01";
  var filtered_data = [];
  var totalDist = 0;
  /**
   * @param {{ access_token: any; }} res
   */
  function getActivities(res) {
    const activities_link = `https://www.strava.com/api/v3/athlete/activities?access_token=${res.access_token}`;
    fetch(activities_link)
      .then((res) => res.json())
      .then(function (data) {
        // 		var map = L.map('map').setView([51.505, -0.09], 13);
        // 		L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
        // 			maxZoom: 19,
        // 			attribution: '&copy; <a href="http://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
        // 		}).addTo(map);
        filtered_data = data.filter(
          // @ts-ignore
          (activity) =>
            compareDate(activity.start_date, streak_start_date) == 1,
        );
        for (var x = 0; x < filtered_data.length; x++) {
          console.log(filtered_data[x]);
          totalDist += filtered_data[x].distance;
        }
        console.log("total distance (in Miles): " + getMiles(totalDist));
        console.log("total distance (in Meters): " + totalDist);
        if (browser) {
          document.getElementById("dist").innerHTML =
            "total distance (in Miles): " +
            Math.round(((getMiles(totalDist) + Number.EPSILON) * 100) / 100);
        }
      });
  }
  function formatDate(s) {
    var year = s.substring(0, 4);
    var month = s.substring(5, 7);
    var day = s.substring(8, 10);
    return month + "/" + day + "/" + year;
  }
  /** CompareDate(s1,s2) returns -1 if s2>s1, and 1 if s1>s2. 0 otherwise */
  function compareDate(s1, s2) {
    let date1 = new Date(formatDate(s1)).getTime();
    let date2 = new Date(formatDate(s2)).getTime();
    if (date1 < date2) {
      return -1;
    } else if (date1 == date2) {
      return 0;
    } else {
      return 1;
    }
  }

  function getMiles(meters) {
    return meters * 0.000621371192;
  }

  function reAuthorize() {
    fetch(auth_link, {
      method: "post",
      headers: {
        Accept: "application/json, text/plain, */*",
        "Content-Type": "application/json",
      },

      body: JSON.stringify({
        client_id: "118455",
        client_secret: "01dfec1b65ed43a13e21fd4cd5b57887708eea67",
        refresh_token: "4580e6ec0472324d897fb45eab904b510b2cda73",
        grant_type: "refresh_token",
      }),
    })
      .then((res) => res.json())
      .then((res) => getActivities(res));
  }

  reAuthorize();
</script>

<head></head>
<body>
  <p id="dist"></p>
</body>
