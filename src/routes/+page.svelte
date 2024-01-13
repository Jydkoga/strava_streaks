<script>
  const auth_link = "https://www.strava.com/api/v3/oauth/token";

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

        for (var x = 0; x < data.length; x++) {
          console.log(data[x]);
        }
      });
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
<body>Hello World! </body>
