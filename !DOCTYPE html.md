  
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Year 11 Inter-House Fantasy Football</title>
  <link href="https://fonts.googleapis.com/css2?family=Outfit:wght@400;600;700;800;900&display=swap" rel="stylesheet">
  <style>
    :root {
      --fire-red: #ef4444;
      --water-blue: #3b82f6;
      --earth-green: #10b981;
      --wind-yellow: #f59e0b;
      --bg-dark: #0f172a;
      --card-bg: #1e293b;
      --card-border: #334155;
      --accent-green: #22c55e;
      --accent-purple: #8b5cf6;
      --text-primary: #f8fafc;
      --text-muted: #94a3b8;
    }

    * { box-sizing: border-box; margin: 0; padding: 0; font-family: 'Outfit', sans-serif; }
    body { background-color: var(--bg-dark); color: var(--text-primary); padding-bottom: 60px; min-height: 100vh; }

    /* Modal Styling */
    .modal-overlay {
      position: fixed; top: 0; left: 0; width: 100vw; height: 100vh;
      background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(8px);
      display: flex; align-items: center; justify-content: center; z-index: 1000;
    }
    .modal-card {
      background: var(--card-bg); border: 2px solid var(--accent-purple);
      border-radius: 16px; padding: 30px; width: 90%; max-width: 450px;
      box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.5); text-align: center;
    }
    .modal-card h2 { font-size: 1.8rem; margin-bottom: 10px; color: #fff; }
    .modal-card p { color: var(--text-muted); font-size: 0.9rem; margin-bottom: 20px; }
    .modal-field { text-align: left; margin-bottom: 15px; }
    .modal-field label { display: block; font-size: 0.8rem; text-transform: uppercase; letter-spacing: 0.05em; color: var(--text-muted); margin-bottom: 5px; font-weight: 700; }
    .modal-field input, .modal-field select {
      width: 100%; padding: 12px; background: #0f172a; border: 1px solid var(--card-border);
      border-radius: 8px; color: #fff; font-size: 1rem; outline: none; transition: 0.2s;
    }
    .modal-field input:focus, .modal-field select:focus { border-color: var(--accent-purple); }

    /* Header & Navigation */
    header {
      background: linear-gradient(135deg, #1e1b4b, #0f172a);
      padding: 20px 15px; text-align: center; border-bottom: 2px solid var(--card-border);
      position: relative;
    }
    header h1 { font-size: 1.9rem; font-weight: 900; letter-spacing: -0.5px; text-transform: uppercase; }
    header h1 span { color: var(--accent-purple); }
    .user-badge {
      display: inline-flex; align-items: center; gap: 8px; margin-top: 10px;
      background: rgba(255,255,255,0.08); padding: 5px 14px; border-radius: 20px;
      font-size: 0.85rem; border: 1px solid rgba(255,255,255,0.1);
    }
    .edit-profile-btn { background: none; border: none; color: var(--accent-purple); cursor: pointer; text-decoration: underline; font-weight: 700; margin-left: 5px; }

    nav {
      display: flex; justify-content: center; gap: 6px; background: #182232; padding: 10px 5px;
      position: sticky; top: 0; z-index: 100; border-bottom: 1px solid var(--card-border); flex-wrap: wrap;
    }
    nav button {
      background: transparent; color: var(--text-muted); border: 1px solid transparent; padding: 8px 14px;
      border-radius: 8px; font-weight: 700; font-size: 0.85rem; cursor: pointer; transition: all 0.2s;
    }
    nav button.active { background: var(--accent-purple); color: #fff; border-color: var(--accent-purple); }
    nav button.admin-tab { border-color: #ef4444; color: #ef4444; }
    nav button.admin-tab.active { background: #ef4444; color: white; }

    .container { max-width: 1100px; margin: 20px auto; padding: 0 15px; }
    .tab-content { display: none; }
    .tab-content.active { display: block; }

    /* Pitch Container */
    .pitch-wrapper {
      background: linear-gradient(180deg, #15803d, #166534);
      border: 4px solid #fff; border-radius: 16px; padding: 20px 10px;
      box-shadow: inset 0 0 50px rgba(0,0,0,0.4); margin-bottom: 25px; position: relative;
    }
    .squad-info-banner {
      background: rgba(0,0,0,0.6); padding: 10px 15px; border-radius: 8px; margin-bottom: 15px;
      display: flex; justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px;
    }
    .pitch-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 12px; }
    
    .squad-slot {
      background: rgba(0,0,0,0.3); border: 2px dashed rgba(255,255,255,0.3); border-radius: 10px;
      min-height: 100px; display: flex; flex-direction: column; align-items: center; justify-content: center;
      padding: 8px; position: relative; text-align: center;
    }
    .squad-slot.filled { border-style: solid; border-color: rgba(255,255,255,0.8); background: rgba(15, 23, 42, 0.85); }

    /* Badges */
    .house-pill { font-size: 0.65rem; font-weight: 800; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; margin-top: 3px; }
    .bg-fire { background: var(--fire-red); color: white; }
    .bg-water { background: var(--water-blue); color: white; }
    .bg-earth { background: var(--earth-green); color: white; }
    .bg-wind { background: var(--wind-yellow); color: #111827; }

    .btn-remove { position: absolute; top: 4px; right: 4px; background: rgba(239,68,68,0.8); color: white; border: none; border-radius: 50%; width: 18px; height: 18px; line-height: 18px; font-size: 10px; cursor: pointer; }

    /* Cards & Tables */
    .player-cards-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(220px, 1fr)); gap: 12px; margin-top: 15px; }
    .card { background: var(--card-bg); border: 1px solid var(--card-border); border-radius: 10px; padding: 12px; }
    .player-card-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 8px; }

    .btn-action { background: var(--accent-green); color: white; border: none; padding: 6px 12px; border-radius: 6px; font-weight: 700; font-size: 0.8rem; cursor: pointer; width: 100%; margin-top: 8px; }
    .btn-action:disabled { background: #475569; cursor: not-allowed; }

    table { width: 100%; border-collapse: collapse; background: var(--card-bg); border-radius: 10px; overflow: hidden; margin-top: 15px; border: 1px solid var(--card-border); }
    th, td { padding: 12px; text-align: left; font-size: 0.9rem; }
    th { background: #1e1b4b; font-weight: 700; color: #c7d2fe; }
    tr:nth-child(even) { background: rgba(255,255,255,0.02); }
    tr { border-bottom: 1px solid var(--card-border); }

    .admin-card { background: var(--card-bg); border: 1px solid var(--card-border); border-radius: 12px; padding: 20px; margin-bottom: 20px; }
    .form-group { margin-bottom: 12px; }
    .form-group label { display: block; font-size: 0.8rem; color: var(--text-muted); margin-bottom: 4px; font-weight: 600; }
    .form-group input, .form-group select { width: 100%; padding: 8px 12px; background: #0f172a; border: 1px solid var(--card-border); border-radius: 6px; color: white; }
  </style>
</head>
<body>

  <!-- ONBOARDING SIGN-UP MODAL -->
  <div id="signup-modal" class="modal-overlay">
    <div class="modal-card">
      <h2>🏆 Fantasy Manager Signup</h2>
      <p>Enter your student details to construct your Year 11 Inter-House Fantasy squad!</p>
      
      <div class="modal-field">
        <label>Your Full Name</label>
        <input type="text" id="user-fullname" placeholder="e.g. Yasin Elkanbany">
      </div>

      <div class="modal-field">
        <label>Year Group</label>
        <select id="user-year">
          <option value="Year 11" selected>Year 11</option>
        </select>
      </div>

      <div class="modal-field">
        <label>Class Section</label>
        <select id="user-section">
          <option value="11A">11A</option>
          <option value="11B">11B</option>
          <option value="11C">11C</option>
          <option value="11D">11D</option>
          <option value="11E">11E</option>
          <option value="11F">11F</option>
          <option value="11G">11G</option>
          <option value="11H">11H</option>
        </select>
      </div>

      <div class="modal-field">
        <label>Fantasy Team Name</label>
        <input type="text" id="user-teamname" placeholder="e.g. Thunder XI">
      </div>

      <div id="signup-error" style="color: #ef4444; font-size: 0.85rem; margin-bottom: 12px; display: none;">Please fill in all fields!</div>

      <button onclick="saveUserProfile()" class="btn-action" style="padding: 12px; font-size: 1rem; background: var(--accent-purple);">Enter Fantasy League</button>
    </div>
  </div>

  <header>
    <h1>Year 11 <span>Inter-House</span> Fantasy</h1>
    <div class="user-badge" id="header-user-badge">
      <span id="badge-name">Guest Manager</span> • <span id="badge-team">No Team</span>
      <button class="edit-profile-btn" onclick="openSignupModal()">Edit</button>
    </div>
  </header>

  <nav>
    <button onclick="switchTab('squad')" class="active">My Squad</button>
    <button onclick="switchTab('stats')">Player Performance</button>
    <button onclick="switchTab('rivals')">Rival Squads</button>
    <button onclick="switchTab('fixtures')">Matches</button>
    <button onclick="switchTab('leaderboard')">Leaderboard</button>
    <button onclick="switchTab('admin')" class="admin-tab">🔒 Admin Panel</button>
  </nav>

  <div class="container">

    <!-- MY SQUAD TAB -->
    <div id="tab-squad" class="tab-content active">
      <div class="squad-info-banner">
        <div>
          <h3 id="squad-team-title">My Fantasy Squad</h3>
          <p style="font-size:0.8rem; color:var(--text-muted);" id="squad-manager-sub">Manager: - | Section: -</p>
        </div>
        <div>
          <span style="font-size:0.85rem; font-weight:700; color:var(--accent-green);" id="squad-count-badge">0 / 9 Players</span>
        </div>
      </div>

      <div id="squad-alert" style="color:#ef4444; font-size:0.85rem; font-weight:700; margin-bottom:10px;"></div>

      <div class="pitch-wrapper">
        <div class="pitch-grid" id="pitch-container"></div>
      </div>

      <button id="save-squad-btn" onclick="saveSquadLocally()" class="btn-action" style="padding: 14px; font-size: 1rem; margin-bottom: 25px;" disabled>
        Save Squad
      </button>

      <h3>Select School Players</h3>
      <p style="font-size:0.85rem; color:var(--text-muted); margin-bottom:10px;">Rule: Exactly 9 players required. Max 3 players from any single house.</p>
      
      <div class="player-cards-grid" id="squad-picker-cards"></div>
    </div>

    <!-- PLAYER STATS TAB -->
    <div id="tab-stats" class="tab-content">
      <h2>Player Performance Index</h2>
      <table>
        <thead>
          <tr>
            <th>Player Name</th>
            <th>House</th>
            <th>Class</th>
            <th>Pos</th>
            <th>Goals</th>
            <th>Assists</th>
            <th>Clean Sheet</th>
            <th>Points</th>
          </tr>
        </thead>
        <tbody id="stats-table-body"></tbody>
      </table>
    </div>

    <!-- RIVAL SQUADS TAB -->
    <div id="tab-rivals" class="tab-content">
      <h2>Rival Fantasy Squads</h2>
      <p style="font-size:0.85rem; color:var(--text-muted); margin-bottom:15px;">Inspect starting lineups built by other students across Year 11.</p>
      <div id="rivals-container" class="player-cards-grid"></div>
    </div>

    <!-- FIXTURES TAB -->
    <div id="tab-fixtures" class="tab-content">
      <h2>Inter-House Schedule</h2>
      <div id="fixtures-container" style="margin-top:15px;"></div>
    </div>

    <!-- LEADERBOARD TAB -->
    <div id="tab-leaderboard" class="tab-content">
      <h2>Fantasy Leaderboard</h2>
      <table>
        <thead>
          <tr>
            <th>Rank</th>
            <th>Manager Name</th>
            <th>Section</th>
            <th>Team Name</th>
            <th>Total Points</th>
          </tr>
        </thead>
        <tbody id="leaderboard-table-body"></tbody>
      </table>
    </div>

    <!-- ADMIN PANEL TAB -->
    <div id="tab-admin" class="tab-content">
      <div id="admin-lock-screen" class="admin-card" style="text-align:center;">
        <h3>🔒 Administrator Passcode Required</h3>
        <p style="font-size:0.85rem; color:var(--text-muted); margin: 10px 0;">Enter admin passcode to update player stats, change positions, and manage matches.</p>
        <input type="password" id="admin-passcode-input" placeholder="Passcode" style="padding:10px; background:#0f172a; border:1px solid var(--card-border); border-radius:6px; color:white; width:200px; text-align:center;">
        <br>
        <button onclick="unlockAdmin()" class="btn-action" style="width:200px; margin-top:10px; background:var(--accent-purple);">Unlock</button>
        <div id="admin-pass-error" style="color:#ef4444; font-size:0.85rem; margin-top:10px; display:none;">Incorrect Passcode!</div>
      </div>

      <div id="admin-content" style="display:none;">
        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:15px;">
          <h2>Admin Control Dashboard</h2>
          <button onclick="lockAdmin()" style="background:#ef4444; color:white; border:none; padding:6px 12px; border-radius:6px; cursor:pointer;">Lock Panel</button>
        </div>

        <div class="admin-card">
          <h3>Manage Player Positions & Stats</h3>
          <table>
            <thead>
              <tr>
                <th>Name</th>
                <th>House</th>
                <th>Position</th>
                <th>Goals</th>
                <th>Assists</th>
                <th>Clean Sheet</th>
                <th>Total Points</th>
              </tr>
            </thead>
            <tbody id="admin-players-table"></tbody>
          </table>
        </div>
      </div>
    </div>

  </div>

  <script>
    const INITIAL_PLAYERS = [
      // Water
      { id: 1, name: "Andreh alhumsi", house: "Water", class: "11H", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 2, name: "Muhammad Ismail A", house: "Water", class: "11B", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 3, name: "Mohammed Karam", house: "Water", class: "11B", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 4, name: "adam osmat nasr", house: "Water", class: "11A", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 5, name: "Aditya Shetty", house: "Water", class: "11H", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 6, name: "Steven Morcos", house: "Water", class: "11D", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 7, name: "Yousuf Zahran", house: "Water", class: "11D", pos: "GK", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 8, name: "Andres Gonzalez", house: "Water", class: "11D", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 9, name: "Aarush Mukherjee", house: "Water", class: "11A", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 10, name: "Mahmoud Ahmed", house: "Water", class: "11C", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 11, name: "Aiden Mendes", house: "Water", class: "11F", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 12, name: "Ahmed Nabil", house: "Water", class: "11A", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },

      // Wind
      { id: 13, name: "Sreedeemanthaa", house: "Wind", class: "11E", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 14, name: "Omar Elgammal", house: "Wind", class: "11A", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 15, name: "Abeer Manohar", house: "Wind", class: "11H", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 16, name: "Muhammad Arham Nave", house: "Wind", class: "11D", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 17, name: "Aiden nidhin", house: "Wind", class: "11H", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 18, name: "Kabir Shah", house: "Wind", class: "11C", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 19, name: "Anas baqai", house: "Wind", class: "11C", pos: "GK", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 20, name: "Sarem ul haq", house: "Wind", class: "11B", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 21, name: "Mohamed Hossam", house: "Wind", class: "11F", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 22, name: "Emir Dilmukhametov", house: "Wind", class: "11G", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 23, name: "Hyder Said Rashid Ali", house: "Wind", class: "11F", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 24, name: "Adam Yousef", house: "Wind", class: "11A", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },

      // Fire
      { id: 25, name: "George Palabekhian", house: "Fire", class: "11A", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 26, name: "Mohammad Mannaa", house: "Fire", class: "11F", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 27, name: "Mohammed Karam Naqvi", house: "Fire", class: "11B", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 28, name: "Jamil samer Mohammad O", house: "Fire", class: "11C", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 29, name: "Malek mohamed", house: "Fire", class: "11F", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 30, name: "Shawn Kundnani", house: "Fire", class: "11C", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 31, name: "Nishant Palan", house: "Fire", class: "11C", pos: "GK", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 32, name: "Alexandros Daouer", house: "Fire", class: "11C", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 33, name: "Mansoor Barakzai", house: "Fire", class: "11C", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 34, name: "Mohamed Mikail Nihad Mu", house: "Fire", class: "11C", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 35, name: "Adam Alkamal", house: "Fire", class: "11C", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 36, name: "Mazin Mohamed", house: "Fire", class: "11F", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },

      // Earth
      { id: 37, name: "Taym mansour", house: "Earth", class: "11G", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 38, name: "Souren najari", house: "Earth", class: "11B", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 39, name: "Yuvaan Shenoy", house: "Earth", class: "11A", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 40, name: "Ahmed Faizan Shah", house: "Earth", class: "11D", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 41, name: "Zeineldin Mandour", house: "Earth", class: "11B", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 42, name: "Jamal jaquavius micheal Jordan", house: "Earth", class: "11G", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 43, name: "aadil", house: "Earth", class: "11H", pos: "GK", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 44, name: "Abdullah Jam", house: "Earth", class: "11H", pos: "MID", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 45, name: "Jeremiah George Sony", house: "Earth", class: "11C", pos: "DEF", goals: 0, assists: 0, cleanSheet: 0, points: 0 },
      { id: 46, name: "Yasin Elkanbany", house: "Earth", class: "11B", pos: "FWD", goals: 0, assists: 0, cleanSheet: 0, points: 0 }
    ];

    let players = JSON.parse(localStorage.getItem('y11_players')) || INITIAL_PLAYERS;
    let mySquad = JSON.parse(localStorage.getItem('y11_mysquad')) || [];
    let userProfile = JSON.parse(localStorage.getItem('y11_profile')) || null;

    function init() {
      if (!userProfile) {
        document.getElementById('signup-modal').style.display = 'flex';
      } else {
        document.getElementById('signup-modal').style.display = 'none';
        updateProfileDisplay();
      }
      renderPitch();
      renderPickerCards();
      renderStats();
      renderRivals();
      renderFixtures();
      renderLeaderboard();
      renderAdminTable();
    }

    function saveUserProfile() {
      const fullname = document.getElementById('user-fullname').value.trim();
      const year = document.getElementById('user-year').value;
      const section = document.getElementById('user-section').value;
      const teamname = document.getElementById('user-teamname').value.trim();

      if (!fullname || !teamname) {
        document.getElementById('signup-error').style.display = 'block';
        return;
      }

      userProfile = { fullname, year, section, teamname };
      localStorage.setItem('y11_profile', JSON.stringify(userProfile));
      document.getElementById('signup-modal').style.display = 'none';
      updateProfileDisplay();
    }

    function openSignupModal() {
      if (userProfile) {
        document.getElementById('user-fullname').value = userProfile.fullname;
        document.getElementById('user-section').value = userProfile.section;
        document.getElementById('user-teamname').value = userProfile.teamname;
      }
      document.getElementById('signup-modal').style.display = 'flex';
    }

    function updateProfileDisplay() {
      if (!userProfile) return;
      document.getElementById('badge-name').innerText = userProfile.fullname;
      document.getElementById('badge-team').innerText = userProfile.teamname;
      document.getElementById('squad-team-title').innerText = userProfile.teamname;
      document.getElementById('squad-manager-sub').innerText = `Manager: ${userProfile.fullname} | Section: ${userProfile.section}`;
    }

    function switchTab(tabId) {
      document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
      document.querySelectorAll('nav button').forEach(el => el.classList.remove('active'));
      document.getElementById(`tab-${tabId}`).classList.add('active');
      event.target.classList.add('active');
    }

    function renderPitch() {
      const pitch = document.getElementById('pitch-container');
      pitch.innerHTML = '';

      for (let i = 0; i < 9; i++) {
        const p = mySquad[i];
        const slot = document.createElement('div');
        slot.className = `squad-slot ${p ? 'filled' : ''}`;
        
        if (p) {
          slot.innerHTML = `
            <button class="btn-remove" onclick="removePlayerFromSquad(${p.id})">✕</button>
            <strong style="font-size:0.85rem; margin-top:6px;">${p.name}</strong>
            <span class="house-pill bg-${p.house.toLowerCase()}">${p.house}</span>
            <small style="color:var(--text-muted); font-size:0.7rem; margin-top:2px;">${p.pos} • ${p.class}</small>
          `;
        } else {
          slot.innerHTML = `<span style="color:rgba(255,255,255,0.4); font-size:0.8rem; font-weight:700;">Slot ${i+1}</span>`;
        }
        pitch.appendChild(slot);
      }

      document.getElementById('squad-count-badge').innerText = `${mySquad.length} / 9 Players`;
      document.getElementById('save-squad-btn').disabled = (mySquad.length !== 9);
    }

    function renderPickerCards() {
      const grid = document.getElementById('squad-picker-cards');
      grid.innerHTML = '';

      players.forEach(p => {
        const isAdded = mySquad.some(item => item.id === p.id);
        const card = document.createElement('div');
        card.className = 'card';
        card.innerHTML = `
          <div class="player-card-header">
            <strong>${p.name}</strong>
            <span class="house-pill bg-${p.house.toLowerCase()}">${p.house}</span>
          </div>
          <p style="font-size:0.8rem; color:var(--text-muted);">Class: ${p.class} | Position: ${p.pos}</p>
          <button class="btn-action" onclick="addPlayerToSquad(${p.id})" ${isAdded ? 'disabled' : ''}>
            ${isAdded ? 'Selected' : 'Add to Squad'}
          </button>
        `;
        grid.appendChild(card);
      });
    }

    function addPlayerToSquad(id) {
      if (mySquad.length >= 9) return;
      const p = players.find(item => item.id === id);
      
      const houseCount = mySquad.filter(item => item.house === p.house).length;
      if (houseCount >= 3) {
        document.getElementById('squad-alert').innerText = `Max 3 players allowed from ${p.house} House!`;
        return;
      }

      document.getElementById('squad-alert').innerText = '';
      mySquad.push(p);
      renderPitch();
      renderPickerCards();
    }

    function removePlayerFromSquad(id) {
      mySquad = mySquad.filter(item => item.id !== id);
      document.getElementById('squad-alert').innerText = '';
      renderPitch();
      renderPickerCards();
    }

    function saveSquadLocally() {
      localStorage.setItem('y11_mysquad', JSON.stringify(mySquad));
      alert('Your Fantasy Squad has been saved successfully!');
    }

    function renderStats() {
      const tbody = document.getElementById('stats-table-body');
      tbody.innerHTML = '';
      players.forEach(p => {
        tbody.innerHTML += `
          <tr>
            <td><strong>${p.name}</strong></td>
            <td><span class="house-pill bg-${p.house.toLowerCase()}">${p.house}</span></td>
            <td>${p.class}</td>
            <td>${p.pos}</td>
            <td>${p.goals}</td>
            <td>${p.assists}</td>
            <td>${p.cleanSheet}</td>
            <td><strong>${p.points} pts</strong></td>
          </tr>
        `;
      });
    }

    function renderRivals() {
      const container = document.getElementById('rivals-container');
      container.innerHTML = `
        <div class="card" style="grid-column: 1/-1;">
          <h3 style="color:var(--accent-purple); margin-bottom:5px;">Leaderboard Rivals Preview</h3>
          <p style="font-size:0.85rem; color:var(--text-muted);">Rival manager squads update dynamically as players submit their teams.</p>
        </div>
      `;
    }

    function renderFixtures() {
      const container = document.getElementById('fixtures-container');
      container.innerHTML = `
        <div class="card" style="margin-bottom:10px;">
          <strong style="color:var(--fire-red);">Fire House</strong> vs <strong style="color:var(--water-blue);">Water House</strong>
          <p style="font-size:0.8rem; color:var(--text-muted); margin-top:4px;">Match 1 • Pitch 1 • Kickoff: Thursday 3:30 PM</p>
        </div>
        <div class="card">
          <strong style="color:var(--wind-yellow);">Wind House</strong> vs <strong style="color:var(--earth-green);">Earth House</strong>
          <p style="font-size:0.8rem; color:var(--text-muted); margin-top:4px;">Match 2 • Pitch 2 • Kickoff: Thursday 4:15 PM</p>
        </div>
      `;
    }

    function renderLeaderboard() {
      const tbody = document.getElementById('leaderboard-table-body');
      tbody.innerHTML = '';
      const totalPoints = mySquad.reduce((sum, p) => sum + p.points, 0);

      const managerName = userProfile ? userProfile.fullname : "Guest";
      const managerSec = userProfile ? userProfile.section : "-";
      const teamName = userProfile ? userProfile.teamname : "My Fantasy Squad";

      tbody.innerHTML = `
        <tr>
          <td><strong>#1</strong></td>
          <td><strong>${managerName}</strong></td>
          <td>${managerSec}</td>
          <td>${teamName}</td>
          <td><strong>${totalPoints} pts</strong></td>
        </tr>
      `;
    }

    function unlockAdmin() {
      const code = document.getElementById('admin-passcode-input').value;
      if (code === '01234567') {
        document.getElementById('admin-lock-screen').style.display = 'none';
        document.getElementById('admin-content').style.display = 'block';
        document.getElementById('admin-pass-error').style.display = 'none';
      } else {
        document.getElementById('admin-pass-error').style.display = 'block';
      }
    }

    function lockAdmin() {
      document.getElementById('admin-lock-screen').style.display = 'block';
      document.getElementById('admin-content').style.display = 'none';
      document.getElementById('admin-passcode-input').value = '';
    }

    function renderAdminTable() {
      const tbody = document.getElementById('admin-players-table');
      tbody.innerHTML = '';
      players.forEach(p => {
        tbody.innerHTML += `
          <tr>
            <td><strong>${p.name}</strong></td>
            <td><span class="house-pill bg-${p.house.toLowerCase()}">${p.house}</span></td>
            <td>
              <select onchange="updatePlayerPos(${p.id}, this.value)" style="padding:4px; background:#0f172a; color:white; border:1px solid var(--card-border); border-radius:4px;">
                <option value="GK" ${p.pos==='GK'?'selected':''}>GK</option>
                <option value="DEF" ${p.pos==='DEF'?'selected':''}>DEF</option>
                <option value="MID" ${p.pos==='MID'?'selected':''}>MID</option>
                <option value="FWD" ${p.pos==='FWD'?'selected':''}>FWD</option>
              </select>
            </td>
            <td><input type="number" value="${p.goals}" onchange="updatePlayerStat(${p.id}, 'goals', this.value)" style="width:50px; padding:4px; background:#0f172a; color:white; border:1px solid var(--card-border); border-radius:4px;"></td>
            <td><input type="number" value="${p.assists}" onchange="updatePlayerStat(${p.id}, 'assists', this.value)" style="width:50px; padding:4px; background:#0f172a; color:white; border:1px solid var(--card-border); border-radius:4px;"></td>
            <td><input type="number" value="${p.cleanSheet}" onchange="updatePlayerStat(${p.id}, 'cleanSheet', this.value)" style="width:50px; padding:4px; background:#0f172a; color:white; border:1px solid var(--card-border); border-radius:4px;"></td>
            <td><strong>${p.points} pts</strong></td>
          </tr>
        `;
      });
    }

    function updatePlayerPos(id, newPos) {
      const p = players.find(item => item.id === id);
      if (p) {
        p.pos = newPos;
        savePlayersAndRefresh();
      }
    }

    function updatePlayerStat(id, field, value) {
      const p = players.find(item => item.id === id);
      if (p) {
        p[field] = parseInt(value) || 0;
        p.points = (p.goals * 5) + (p.assists * 3) + (p.cleanSheet * 4);
        savePlayersAndRefresh();
      }
    }

    function savePlayersAndRefresh() {
      localStorage.setItem('y11_players', JSON.stringify(players));
      renderStats();
      renderLeaderboard();
      renderPickerCards();
      renderAdminTable();
    }

    window.onload = init;
  </script>
</body>
</html>

```
