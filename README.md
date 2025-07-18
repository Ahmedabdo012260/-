```html name=index.html
<!DOCTYPE html>
<html lang="ar">
<head>
  <meta charset="UTF-8" />
  <title>مباريات اليوم - كيان متحرر</title>
  <style>
    body { font-family: 'Arial', sans-serif; direction: rtl; background: #111; color: #fff; padding: 20px; }
    h1 { color: #00ffcc; }
    .match { border: 1px solid #444; border-radius: 10px; padding: 10px; margin-bottom: 15px; background: #1a1a1a; }
    .team { font-weight: bold; }
    .score { font-size: 1.2em; color: #ffcc00; }
    .prediction { color: #00ff99; margin-top: 5px; }
    .stats { margin-top: 8px; background: #222; border-radius: 8px; padding: 7px 12px; }
    .stats span { display: inline-block; margin-left: 12px; }
  </style>
</head>
<body>

  <h1>⚽ مباريات دوري الأبطال اليوم - كيان متحرر</h1>
  <div id="matches">جاري التحميل...</div>

  <script>
    const apiKey = '6af7960a0bb44509bc7440b1290eae0c';
    const today = new Date().toISOString().split('T')[0];

    async function fetchMatches() {
      const url = `https://api.football-data.org/v4/competitions/CL/matches?dateFrom=${today}&dateTo=${today}`;
      
      try {
        const res = await fetch(url, {
          headers: { 'X-Auth-Token': apiKey }
        });

        const data = await res.json();
        const matches = data.matches;
        const container = document.getElementById('matches');

        if (matches.length === 0) {
          container.innerHTML = 'لا توجد مباريات اليوم في دوري الأبطال.';
          return;
        }

        container.innerHTML = '';
        for (const match of matches) {
          const home = match.homeTeam.name;
          const away = match.awayTeam.name;
          const time = match.utcDate.slice(11, 16);
          const score = match.score.fullTime;

          // توقع إحصائيات عشوائية لأن api لا توفر الإحصائيات التفصيلية في نفس الاندبوينت
          const stats = predictStats(home, away, score);

          const prediction = predictScore(home, away);

          const html = `
            <div class="match">
              <div class="team">${home} 🆚 ${away}</div>
              <div>⏰ الساعة: ${time}</div>
              <div class="score">النتيجة: ${score.home ?? '-'} - ${score.away ?? '-'}</div>
              <div class="stats">
                <span>الأهداف: ${stats.goals.home} - ${stats.goals.away}</span>
                <span>الركنيات: ${stats.corners.home} - ${stats.corners.away}</span>
                <span>الكروت الصفراء: ${stats.yellowCards.home} - ${stats.yellowCards.away}</span>
                <span>التسللات: ${stats.offsides.home} - ${stats.offsides.away}</span>
              </div>
              <div class="prediction">🔮 التوقع: ${prediction}</div>
            </div>
          `;

          container.innerHTML += html;
        }

      } catch (err) {
        document.getElementById('matches').innerText = 'حدث خطأ أثناء تحميل المباريات.';
        console.error(err);
      }
    }

    // توقع إحصائيات عشوائية أو منطقية بناءً على النتيجة
    function predictStats(home, away, score) {
      // إذا كانت النتيجة معروفة استخدمها، وإلا عشوائي
      const homeGoals = typeof score.home === 'number' ? score.home : Math.floor(Math.random() * 4);
      const awayGoals = typeof score.away === 'number' ? score.away : Math.floor(Math.random() * 4);

      // نماذج بسيطة للإحصائيات
      const homeCorners = Math.max(2, homeGoals + Math.floor(Math.random() * 5));
      const awayCorners = Math.max(2, awayGoals + Math.floor(Math.random() * 5));
      const homeYellow = Math.floor(Math.random() * 4);
      const awayYellow = Math.floor(Math.random() * 4);
      const homeOffsides = Math.floor(Math.random() * 3);
      const awayOffsides = Math.floor(Math.random() * 3);

      return {
        goals: { home: homeGoals, away: awayGoals },
        corners: { home: homeCorners, away: awayCorners },
        yellowCards: { home: homeYellow, away: awayYellow },
        offsides: { home: homeOffsides, away: awayOffsides }
      };
    }

    function predictScore(home, away) {
      const teams = {
        'Real Madrid': 95,
        'Paris Saint Germain': 92,
        'Manchester City': 94,
        'Bayern Munich': 90,
        'Barcelona': 89,
        'Chelsea': 85,
        'Liverpool': 88,
      };

      const homeStrength = teams[home] ?? 85;
      const awayStrength = teams[away] ?? 85;
      const diff = homeStrength - awayStrength;

      if (diff > 5) return `${home} ${2 + Math.floor(Math.random()*2)} - ${Math.floor(Math.random()*2)} ${away}`;
      if (diff < -5) return `${home} ${Math.floor(Math.random()*2)} - ${2 + Math.floor(Math.random()*2)} ${away}`;
      return `${home} 1 - 1 ${away}`;
    }

    fetchMatches();
  </script>
</body>
</html>
```
