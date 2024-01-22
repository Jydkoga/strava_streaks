<script>
  // @ts-nocheck

  import { browser } from "$app/environment";

  const auth_link = "https://www.strava.com/api/v3/oauth/token";
  var filtered_data = [];
  var totalDist = 0;
  var streak_start_date = "2024-01-01";
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
        var totActivities = filtered_data.length;
        for (var x = 0; x < totActivities; x++) {
          console.log(filtered_data[x]);
          totalDist += filtered_data[x].distance;
        }
        console.log("total distance (in Miles): " + getMiles(totalDist));
        console.log("total distance (in Meters): " + totalDist);

        if (browser) {
          getDist();
          getTotalActivities(totActivities);
          getStart(streak_start_date);
          console.log("Start Streak Date is: " + streak_start_date);
          const streakElement = document.getElementById("startStreak");
          streakElement.addEventListener("click", setStreakStart);
        }
      });
  }
  /**getStart(streak_start_date) takes in the streak start date and updates that value within HTML with id="start"*/
  function getStart(streak_start_date) {
    document.getElementById("start").innerHTML =
      "You started your streaks on: " + streak_start_date;
  }
  /** getDist() updates total distance in HTML with id="dist"*/
  function getDist() {
    document.getElementById("dist").innerHTML =
      "Total dist (in Miles): " + getMiles(totalDist);
  }
  /**getTotalActivities(totActivities) takes in total number of activities and updates that value within HTML with id="totActivities"*/
  function getTotalActivities(totActivities) {
    document.getElementById("totActivities").innerHTML =
      "Total number of activities: " + totActivities;
  }

  /**formatMonth(month) takes in a num (Date representation of a month) and converts it to an acceptabel format of either 0X or XX*/
  function formatMonth(month) {
    if (month < 10) {
      return "0" + month;
    } else {
      return "" + month;
    }
  }
  /**setStreakStart() sets streak_start_date to the current date recorded by the browser*/
  function setStreakStart() {
    var currentDate = new Date();
    currentDate =
      currentDate.getFullYear() +
      "-" +
      //TODO: have to reformat to 0X for single digit
      formatMonth(currentDate.getMonth() + 1) +
      "-" +
      currentDate.getDate();
    streak_start_date = currentDate;
    console.log("Start Streak Date is now: " + streak_start_date);
    getStart(streak_start_date);
  }
  /**formatDate(s) takes in a string in the format year-month-day and returns an instance of Date*/
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
  /**getMiles(meters) converts meters to miles, rounded*/
  function getMiles(meters) {
    return Math.round(((meters * 0.000621371192 + Number.EPSILON) * 100) / 100);
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

<head><title>Strava Streaks</title></head>
<body>
  <div class="description">
    <h1>Welcome to Strava Streaks!</h1>
    <h2>Enforce your discipline in a fun way!</h2>
  </div>

  <div class="stats">
    <h2>Your Stats</h2>
    <p id="start"></p>
    <p id="dist"></p>
    <p id="totActivities"></p>
  </div>
  <button id="startStreak" class="start_streak">Start Streak?</button>
</body>

<style>
  body {
    display: flex;
    flex-direction: column;
  }
  .description {
    display: flex;
    flex-direction: column;
  }
  .start_streak {
    border: none;
    background: none;
    border-radius: 5px;
    background-color: lightgoldenrodyellow;
  }
  .start_streak:hover {
    cursor: pointer;
    background-color: gold;
  }
</style>
