<!DOCTYPE html>
<html lang="ja">
<head>
  <meta charset="UTF-8">
  <title>ハンドボールスコアシート</title>
  <style>
    body { font-family: sans-serif; padding: 20px; }
    button { margin: 5px; padding: 10px; font-size: 14px; white-space: nowrap; }
    table { border-collapse: collapse; margin-top: 20px; width: 48%; float: left; }
    th, td { border: 1px solid #ccc; padding: 8px; text-align: center; }
    .section { margin-bottom: 20px; clear: both; }
    input[type="text"] { font-size: 14px; padding: 5px; margin: 5px; }
    #controls { margin-bottom: 20px; }
    #recordTables { display: flex; justify-content: space-between; }
    #endControls { clear: both; margin-top: 40px; text-align: center; }
    .score-grid { display: grid; grid-template-columns: repeat(3, 1fr); grid-template-rows: repeat(3, auto); width: 300px; margin-bottom: 10px; }
    .score-extra { display: flex; flex-wrap: wrap; gap: 5px; }
    .button-grid-5 { display: grid; grid-template-columns: repeat(5, 1fr); gap: 5px; max-width: 400px; margin-bottom: 10px; }
    .button-grid-auto { display: flex; flex-wrap: wrap; gap: 5px; max-width: 600px; }
  </style>
</head>
<body>
  <h1>ハンドボールスコアシート</h1>

  <div class="section">
    <strong>チーム名:</strong><br>
    <input type="text" id="teamAInput" value="自チーム"> 
    <input type="text" id="teamBInput" value="相手チーム"> 
    <button onclick="startTimer()">試合開始</button>
  </div>

  <div class="section">
    <strong>記録チームを選択:</strong><br>
    <button onclick="setTeam('A')">左側のチーム</button>
    <button onclick="setTeam('B')">右側のチーム</button>
    <span id="teamDisplay"></span>
  </div>  <div class="section">
    <strong>背番号:</strong><br>
    <div class="button-grid-5">
      <script>
        for (let i = 1; i <= 20; i++) {
          document.write(`<button onclick="setNumber(${i})">${i}</button>`);
        }
      </script>
    </div>
    <span id="numberDisplay"></span>
  </div>

  <div class="section">
    <strong>シュート種類:</strong><br>
    <div class="button-grid-auto">
      <button onclick="setType('long-middle')">long-middle</button>
      <button onclick="setType('cut in')">cut in</button>
      <button onclick="setType('cut out')">cut out</button>
      <button onclick="setType('wing')">wing</button>
      <button onclick="setType('pivot')">pivot</button>
      <button onclick="setType('others')">others</button>
      <button onclick="setType('counter')">counter</button>
      <button onclick="setType('PT')">PT</button>
      <button onclick="setType('stepshoot')">stepshoot</button>
    </div>
    <span id="typeDisplay"></span>
  </div>

  <div class="section">
    <strong>シュート位置:</strong><br>
    <div class="button-grid-auto">
      <button onclick="setPosition('6mセンター')">6mセンター</button>
      <button onclick="setPosition('6m正45')">6m正45</button>
      <button onclick="setPosition('6m逆45')">6m逆45</button>
      <button onclick="setPosition('9mセンター')">9mセンター</button>
      <button onclick="setPosition('9m正45')">9m正45</button>
      <button onclick="setPosition('9m逆45')">9m逆45</button>
      <button onclick="setPosition('正サイド')">正サイド</button>
      <button onclick="setPosition('逆サイド')">逆サイド</button>
    </div>
    <span id="positionDisplay"></span>
  </div>

  <div class="section">
    <strong>スコア:</strong><br>
    <div class="score-grid">
      <button onclick="setScore('左上')">左上</button>
      <button onclick="setScore('真ん中上')">真ん中上</button>
      <button onclick="setScore('右上')">右上</button>
      <button onclick="setScore('左真ん中')">左真ん中</button>
      <button onclick="setScore('真ん中')">真ん中</button>
      <button onclick="setScore('右真ん中')">右真ん中</button>
      <button onclick="setScore('左下')">左下</button>
      <button onclick="setScore('真ん中下')">真ん中下</button>
      <button onclick="setScore('右下')">右下</button>
    </div>
    <div class="score-extra">
      <button onclick="setScore('lob')">lob</button>
      <button onclick="setScore('wide')">wide</button>
      <button onclick="setScore('post-bar')">post-bar</button>
      <button onclick="setScore('block')">block</button>
    </div>
    <span id="scoreDisplay"></span>
  </div>  <div class="section" id="controls">
    <h3>試合時間: <span id="matchTime">00:00</span></h3>
    <button onclick="pauseTimer()">ストップ</button>
    <button onclick="recordTimeout()">タイムアウト</button>
    <h3>現在の得点: <span id="scoreCounter">0 - 0</span></h3>
    <button onclick="addRecord()">記録</button>
    <button onclick="undoLastRecord()">直前の記録を取消</button>
  </div>

  <div id="recordTables">
    <table>
      <caption><strong id="teamAName">自チーム</strong></caption>
      <thead>
        <tr><th>時間</th><th>背番号</th><th>種類</th><th>位置</th><th>スコア</th></tr>
      </thead>
      <tbody id="tableA"></tbody>
    </table>

    <table>
      <caption><strong id="teamBName">相手チーム</strong></caption>
      <thead>
        <tr><th>時間</th><th>背番号</th><th>種類</th><th>位置</th><th>スコア</th></tr>
      </thead>
      <tbody id="tableB"></tbody>
    </table>
  </div>

  <div class="section">
    <h3>シュート位置別 成功率（自チーム）</h3>
    <table>
      <thead>
        <tr><th>位置</th><th>成功数</th><th>試行数</th><th>成功率</th></tr>
      </thead>
      <tbody id="positionStats"></tbody>
    </table>
  </div>

  <div id="endControls">
    <button onclick="window.print()">記録終了（PDF保存）</button>
  </div>

</body> <script>
  let currentTeam = '';
  let currentNumber = '';
  let currentType = '';
  let currentPosition = '';
  let currentScore = '';
  let timer;
  let startTime;
  let running = false;
  let teamAScore = 0;
  let teamBScore = 0;
  const recordsA = [];

  function setTeam(team) {
    currentTeam = team;
    document.getElementById('teamDisplay').textContent = team === 'A' ? '自チーム' : '相手チーム';
  }

  function setNumber(number) {
    currentNumber = number;
    document.getElementById('numberDisplay').textContent = number;
  }

  function setType(type) {
    currentType = type;
    document.getElementById('typeDisplay').textContent = type;
  }

  function setPosition(position) {
    currentPosition = position;
    document.getElementById('positionDisplay').textContent = position;
  }

  function setScore(score) {
    currentScore = score;
    document.getElementById('scoreDisplay').textContent = score;
  }

  function startTimer() {
    if (!running) {
      startTime = Date.now();
      timer = setInterval(updateTime, 1000);
      running = true;
    }
  }

  function updateTime() {
    const elapsed = Math.floor((Date.now() - startTime) / 1000);
    const minutes = String(Math.floor(elapsed / 60)).padStart(2, '0');
    const seconds = String(elapsed % 60).padStart(2, '0');
    document.getElementById('matchTime').textContent = `${minutes}:${seconds}`;
  }

  function pauseTimer() {
    clearInterval(timer);
    running = false;
  }

  function recordTimeout() {
    const time = document.getElementById('matchTime').textContent;
    const row = document.createElement('tr');
    const td = document.createElement('td');
    td.colSpan = 5;
    td.textContent = `${currentTeam === 'A' ? '自チーム' : '相手チーム'} タイムアウト (${time})`;
    row.appendChild(td);
    if (currentTeam === 'A') {
      document.getElementById('tableA').appendChild(row);
    } else if (currentTeam === 'B') {
      document.getElementById('tableB').appendChild(row);
    }
  }

  function addRecord() {
    const time = document.getElementById('matchTime').textContent;
    const row = document.createElement('tr');
    [time, currentNumber, currentType, currentPosition, currentScore].forEach(text => {
      const td = document.createElement('td');
      td.textContent = text;
      row.appendChild(td);
    });

    if (currentTeam === 'A') {
      document.getElementById('tableA').appendChild(row);
      recordsA.push({position: currentPosition, score: currentScore});
      if (!['wide', 'post-bar', 'block'].includes(currentScore)) teamAScore++;
    } else if (currentTeam === 'B') {
      document.getElementById('tableB').appendChild(row);
      if (!['wide', 'post-bar', 'block'].includes(currentScore)) teamBScore++;
    }

    updateScoreDisplay();
    updatePositionStats();
  }

  function undoLastRecord() {
    if (currentTeam === 'A') {
      const table = document.getElementById('tableA');
      if (table.lastChild) {
        table.removeChild(table.lastChild);
        const last = recordsA.pop();
        if (last && !['wide', 'post-bar', 'block'].includes(last.score)) teamAScore--;
      }
    } else if (currentTeam === 'B') {
      const table = document.getElementById('tableB');
      if (table.lastChild) {
        const lastScore = table.lastChild.cells[4]?.textContent;
        table.removeChild(table.lastChild);
        if (lastScore && !['wide', 'post-bar', 'block'].includes(lastScore)) teamBScore--;
      }
    }
    updateScoreDisplay();
    updatePositionStats();
  }

  function updateScoreDisplay() {
    document.getElementById('scoreCounter').textContent = `${teamAScore} - ${teamBScore}`;
  }

  function updatePositionStats() {
    const counts = {};
    recordsA.forEach(r => {
      if (!counts[r.position]) counts[r.position] = { made: 0, total: 0 };
      counts[r.position].total++;
      if (!['wide', 'post-bar', 'block'].includes(r.score)) counts[r.position].made++;
    });

    const tbody = document.getElementById('positionStats');
    tbody.innerHTML = '';
    Object.entries(counts).forEach(([pos, { made, total }]) => {
      const row = document.createElement('tr');
      row.innerHTML = `
        <td>${pos}</td>
        <td>${made}</td>
        <td>${total}</td>
        <td>${(made / total * 100).toFixed(1)}%</td>
      `;
      tbody.appendChild(row);
    });
  }
</script>
