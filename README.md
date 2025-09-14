<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AI-Powered Sports Talent Assessment</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: linear-gradient(to right, #1e3c72, #2a5298);
      color: white;
      text-align: center;
      padding: 20px;
    }
    .container {
      background: rgba(255, 255, 255, 0.1);
      border-radius: 15px;
      padding: 20px;
      max-width: 700px;
      margin: auto;
      box-shadow: 0px 4px 15px rgba(0,0,0,0.4);
    }
    h1 {
      margin-bottom: 20px;
    }
    label {
      display: block;
      margin-top: 10px;
      font-weight: bold;
    }
    input {
      padding: 8px;
      width: 80%;
      border: none;
      border-radius: 8px;
      margin-top: 5px;
      text-align: center;
    }
    button {
      margin-top: 20px;
      padding: 10px 20px;
      border: none;
      border-radius: 8px;
      background: #ff9800;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }
    button:hover {
      background: #e68900;
    }
    #result, #aiSuggestion {
      margin-top: 20px;
      font-size: 18px;
      font-weight: bold;
    }
    canvas {
      margin-top: 30px;
    }
    .watermark {
      position: fixed;
      top: 20px;
      right: 20px;
      font-size: 24px;
      font-weight: bold;
      color: rgba(255,255,255,0.3);
      transform: rotate(-15deg);
      pointer-events: none;
    }
    /* ✅ New styling for Exercise Plan */
    #exercisePlan {
      margin-top: 20px;
      padding: 15px;
      background: rgba(0,0,0,0.7); /* dark background */
      border-radius: 12px;
      color: #f5f5f5; /* light grey text */
      text-align: left;
    }
    #exercisePlan h3 {
      color: #ffcc00; /* yellow heading */
      margin-bottom: 10px;
    }
    #exercisePlan ul {
      margin: 0;
      padding-left: 20px;
    }
    #exercisePlan li {
      margin-bottom: 6px;
    }
  </style>
</head>
<body>
  <div class="watermark">WINNERS</div>
  <div class="container">
    <h1>AI-Powered Sports Talent Assessment</h1>
    
    <label>Height (cm):</label>
    <input type="number" id="height" placeholder="e.g. 175">
    
    <label>Weight (kg):</label>
    <input type="number" id="weight" placeholder="e.g. 70">
    
    <label>100m Sprint Time (seconds):</label>
    <input type="number" id="sprint" placeholder="e.g. 14">
    
    <label>Push-ups in 1 minute:</label>
    <input type="number" id="pushups" placeholder="e.g. 40">
    
    <label>Stamina (1-10 self rating):</label>
    <input type="number" id="stamina" min="1" max="10" placeholder="e.g. 7">
    
    <button onclick="calculateTalent()">Check Talent</button>
    
    <div id="result"></div>
    <canvas id="talentChart"></canvas>
    <div id="aiSuggestion"></div>
    <div id="exercisePlan"></div>
  </div>

  <script>
    function calculateTalent() {
      let height = parseFloat(document.getElementById("height").value);
      let weight = parseFloat(document.getElementById("weight").value);
      let sprint = parseFloat(document.getElementById("sprint").value);
      let pushups = parseFloat(document.getElementById("pushups").value);
      let stamina = parseFloat(document.getElementById("stamina").value);
      
      if (!height || !weight || !sprint || !pushups || !stamina) {
        document.getElementById("result").innerHTML = "⚠️ Please enter all values.";
        return;
      }
      
      // BMI Calculation
      let bmi = weight / ((height/100) ** 2);
      let bmiScore = (bmi >= 18.5 && bmi <= 24.9) ? 25 : 15;
      
      // Sprint Scoring
      let sprintScore = sprint < 13 ? 25 : sprint < 15 ? 18 : 10;
      
      // Pushups Scoring
      let pushupScore = pushups > 50 ? 25 : pushups > 30 ? 18 : 10;
      
      // Stamina Scoring
      let staminaScore = stamina >= 8 ? 25 : stamina >= 5 ? 18 : 10;
      
      let totalScore = bmiScore + sprintScore + pushupScore + staminaScore;
      
      document.getElementById("result").innerHTML = 
        `Your Total Talent Score: <b>${totalScore}/100</b>`;
      
      // Radar Chart
      let ctx = document.getElementById('talentChart').getContext('2d');
      if(window.talentRadar) window.talentRadar.destroy();
      
      window.talentRadar = new Chart(ctx, {
        type: 'radar',
        data: {
          labels: ['BMI', 'Sprint', 'Strength', 'Stamina'],
          datasets: [{
            label: 'Your Performance',
            data: [bmiScore, sprintScore, pushupScore, staminaScore],
            backgroundColor: 'rgba(255, 152, 0, 0.3)',
            borderColor: '#ff9800',
            borderWidth: 2
          }]
        },
        options: {
          scales: { r: { suggestedMin: 0, suggestedMax: 30 } }
        }
      });
      
      // AI-like Suggestions
      let suggestion = "";
      let exercisePlan = "";
      if (sprintScore >= 20 && staminaScore >= 20) {
        suggestion = "🏃 You are best suited for Athletics (Running / Sprinting).";
        exercisePlan = `
          <h3>🏃 7-Day Training Plan for Runners</h3>
          <ul>
            <li><b>Mon:</b> 5km light run + stretching</li>
            <li><b>Tue:</b> Sprint intervals (10 × 100m)</li>
            <li><b>Wed:</b> Strength training (legs & core)</li>
            <li><b>Thu:</b> Rest or yoga</li>
            <li><b>Fri:</b> Hill sprints (8 reps)</li>
            <li><b>Sat:</b> Long run (8–10km)</li>
            <li><b>Sun:</b> Active recovery walk</li>
          </ul>`;
      } else if (pushupScore >= 20 && staminaScore >= 18) {
        suggestion = "💪 You are fit for Strength Sports (Football, Wrestling, Gymnastics).";
        exercisePlan = `
          <h3>💪 7-Day Strength Plan</h3>
          <ul>
            <li><b>Mon:</b> Push-ups, pull-ups, squats</li>
            <li><b>Tue:</b> Core & abs workout</li>
            <li><b>Wed:</b> Full-body strength (dumbbells/bodyweight)</li>
            <li><b>Thu:</b> Rest</li>
            <li><b>Fri:</b> Explosive drills (box jumps, burpees)</li>
            <li><b>Sat:</b> Football/Gymnastics skill practice</li>
            <li><b>Sun:</b> Stretching + yoga</li>
          </ul>`;
      } else if (bmiScore >= 20 && staminaScore >= 18) {
        suggestion = "⚽ You could excel in Team Sports (Football, Basketball).";
        exercisePlan = `
          <h3>⚽ 7-Day Team Sport Plan</h3>
          <ul>
            <li><b>Mon:</b> Passing & dribbling drills</li>
            <li><b>Tue:</b> Agility ladder + sprints</li>
            <li><b>Wed:</b> Upper body workout</li>
            <li><b>Thu:</b> Rest</li>
            <li><b>Fri:</b> Team scrimmage practice</li>
            <li><b>Sat:</b> Endurance run (5–7km)</li>
            <li><b>Sun:</b> Recovery & mobility drills</li>
          </ul>`;
      } else {
        suggestion = "🎯 Focus on general fitness training before choosing a specific sport.";
        exercisePlan = `
          <h3>🏋 General 7-Day Fitness Plan</h3>
          <ul>
            <li><b>Mon:</b> Light cardio + stretching</li>
            <li><b>Tue:</b> Basic strength (push-ups, squats)</li>
            <li><b>Wed:</b> Core strengthening</li>
            <li><b>Thu:</b> Rest</li>
            <li><b>Fri:</b> Jogging (3km)</li>
            <li><b>Sat:</b> Mixed cardio + light weights</li>
            <li><b>Sun:</b> Yoga & mobility</li>
          </ul>`;
      }
      
      document.getElementById("aiSuggestion").innerHTML = `<b>AI Suggestion:</b> ${suggestion}`;
      document.getElementById("exercisePlan").innerHTML = exercisePlan;
    }
  </script>
</body>
</html>
