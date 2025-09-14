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
    #result, #aiSuggestion, #exercisePlan {
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
    table {
      width: 100%;
      margin-top: 20px;
      border-collapse: collapse;
      color: white;
    }
    th, td {
      border: 1px solid rgba(255,255,255,0.3);
      padding: 10px;
      text-align: center;
    }
    th {
      background: rgba(255,255,255,0.2);
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
    <button id="downloadPDF" style="display:none;" onclick="downloadPDF()">Download Report (PDF)</button>
    
    <div id="result"></div>
    <canvas id="talentChart"></canvas>
    <div id="aiSuggestion"></div>
    <div id="exercisePlan"></div>
  </div>

  <script>
    let latestData = {}; // store results for PDF
    
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
      let category = "";
      if (totalScore >= 80) {
        category = "Elite";
        suggestion = "🏆 You have excellent fitness levels! Best suited for Competitive Athletics, Football, or Basketball.";
      } else if (totalScore >= 60) {
        category = "Intermediate";
        suggestion = "⚡ You are fit and can train for Team Sports like Football, Cricket, or Volleyball.";
      } else {
        category = "Beginner";
        suggestion = "💡 You need general fitness training before moving to specific sports. Focus on running, basic strength, and stamina.";
      }
      document.getElementById("aiSuggestion").innerHTML = 
        `<b>AI Suggestion:</b> ${suggestion}`;
      
      // Exercise Plans
      let plan = "";
      if (category === "Beginner") {
        plan = `
        <h2>🏋️ Beginner 7-Day Exercise Plan</h2>
        <table>
          <tr><th>Day</th><th>Workout</th></tr>
          <tr><td>Mon</td><td>Light Jog (15 min) + 3x10 Pushups</td></tr>
          <tr><td>Tue</td><td>Bodyweight Squats 3x12 + Plank 3x20s</td></tr>
          <tr><td>Wed</td><td>Brisk Walk (30 min)</td></tr>
          <tr><td>Thu</td><td>Jog (20 min) + 3x15 Crunches</td></tr>
          <tr><td>Fri</td><td>Pushups 3x12 + Jumping Jacks 3x20</td></tr>
          <tr><td>Sat</td><td>Stretching + Yoga (30 min)</td></tr>
          <tr><td>Sun</td><td>Rest</td></tr>
        </table>`;
      } else if (category === "Intermediate") {
        plan = `
        <h2>⚡ Intermediate 7-Day Exercise Plan</h2>
        <table>
          <tr><th>Day</th><th>Workout</th></tr>
          <tr><td>Mon</td><td>HIIT Run (20 min) + Pushups 4x15</td></tr>
          <tr><td>Tue</td><td>Bodyweight Circuit (Squats, Lunges, Dips)</td></tr>
          <tr><td>Wed</td><td>Jog (30 min) + Core Workout</td></tr>
          <tr><td>Thu</td><td>Sprint Intervals (8x100m)</td></tr>
          <tr><td>Fri</td><td>Strength (Pushups, Planks, Burpees)</td></tr>
          <tr><td>Sat</td><td>Agility Drills + Sport Practice</td></tr>
          <tr><td>Sun</td><td>Rest / Light Stretching</td></tr>
        </table>`;
      } else {
        plan = `
        <h2>🏆 Elite 7-Day Exercise Plan</h2>
        <table>
          <tr><th>Day</th><th>Workout</th></tr>
          <tr><td>Mon</td><td>Sprint Training (10x100m) + Strength Drills</td></tr>
          <tr><td>Tue</td><td>Resistance Training (Gym) + Core Stability</td></tr>
          <tr><td>Wed</td><td>Endurance Run (45 min)</td></tr>
          <tr><td>Thu</td><td>Sport-Specific Drills (Football/Basketball)</td></tr>
          <tr><td>Fri</td><td>HIIT + Plyometric Training</td></tr>
          <tr><td>Sat</td><td>Agility Ladder + Jump Training</td></tr>
          <tr><td>Sun</td><td>Active Recovery (Yoga/Swimming)</td></tr>
        </table>`;
      }
      document.getElementById("exercisePlan").innerHTML = plan;
      
      // Store latest results for PDF
      latestData = { height, weight, sprint, pushups, stamina, bmi: bmi.toFixed(2), totalScore, suggestion, plan };
      document.getElementById("downloadPDF").style.display = "inline-block";
    }
    
    async function downloadPDF() {
      const { jsPDF } = window.jspdf;
      const doc = new jsPDF();
      
      doc.setFontSize(18);
      doc.text("AI-Powered Sports Talent Report", 20, 20);
      
      doc.setFontSize(12);
      doc.text(`Height: ${latestData.height} cm`, 20, 40);
      doc.text(`Weight: ${latestData.weight} kg`, 20, 50);
      doc.text(`100m Sprint: ${latestData.sprint} sec`, 20, 60);
      doc.text(`Push-ups/min: ${latestData.pushups}`, 20, 70);
      doc.text(`Stamina: ${latestData.stamina}/10`, 20, 80);
      doc.text(`BMI: ${latestData.bmi}`, 20, 90);
      doc.text(`Talent Score: ${latestData.totalScore}/100`, 20, 100);
      
      doc.text("AI Suggestion:", 20, 120);
      doc.text(latestData.suggestion.replace(/<[^>]+>/g, ''), 20, 130, { maxWidth: 170 });
      
      // Add Radar Chart as Image
      let chartCanvas = document.getElementById("talentChart");
      let chartImage = chartCanvas.toDataURL("image/png", 1.0);
      doc.addImage(chartImage, "PNG", 110, 40, 80, 80);
      
      doc.addPage();
      doc.setFontSize(16);
      doc.text("7-Day Exercise Plan", 20, 20);
      
      doc.setFontSize(12);
      doc.text("Refer to web view for full detailed table.", 20, 40);
      
      doc.save("Talent_Report.pdf");
    }
  </script>
</body>
</html>
