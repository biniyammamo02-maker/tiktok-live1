<!DOCTYPE html>
<html lang="am">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TikTok Live Connected</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; background: #010101; color: white; text-align: center; margin: 0; padding: 0; padding-bottom: 80px; overflow-x: hidden; }
        h1 { color: #fe2c55; font-size: 24px; font-weight: 800; margin-top: 15px; }
        h2 { margin: 0 0 10px 0; font-size: 20px; }
        .auth-container { max-width: 360px; margin: 60px auto; background: #161618; border: 2px solid #2f2f2f; padding: 30px; border-radius: 12px; box-sizing: border-box; }
        .auth-input { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #2f2f2f; background: #2f2f2f; color: white; border-radius: 20px; outline: none; text-align: center; font-size: 16px; box-sizing: border-box; }
        .auth-btn { width: 100%; padding: 12px; background: #fe2c55; color: white; border: none; border-radius: 20px; font-weight: bold; cursor: pointer; margin-top: 15px; box-sizing: border-box; }
        .auth-btn:hover { background: #e02447; }
        .toggle-link { color: #00f2fe; cursor: pointer; display: block; margin-top: 15px; font-size: 14px; }
        #mainApp { display: none; padding: 10px; }
        .page-content { display: none; min-height: 500px; width: 100%; }
        .page-content.active { display: block; }
        .container { display: flex; justify-content: center; gap: 20px; max-width: 900px; margin: 10px auto; flex-wrap: wrap; }
        .video-box { position: relative; background: #121212; width: 340px; height: 480px; border-radius: 12px; border: 2px solid #2f2f2f; overflow: hidden; display: flex; align-items: center; justify-content: center; }
        canvas { width: 100%; height: 100%; object-fit: cover; position: absolute; top: 0; left: 0; }
        #chat-box { width: 320px; height: 480px; background: #161618; border-radius: 12px; border: 2px solid #2f2f2f; display: flex; flex-direction: column; }
        #messages { flex-grow: 1; overflow-y: auto; padding: 15px; text-align: left; font-size: 14px; height: 380px; }
        .comment-input-area { display: flex; padding: 10px; gap: 10px; background: #010101; border-top: 1px solid #2f2f2f; }
        .comment-input { flex-grow: 1; padding: 12px; border: 1px solid #2f2f2f; background: #2f2f2f; color: white; border-radius: 20px; outline: none; }
        .send-btn { padding: 12px 20px; background: #fe2c55; color: white; border: none; border-radius: 20px; font-weight: bold; cursor: pointer; }
        .live-badge { position: absolute; top: 15px; left: 15px; background: #fe2c55; color: white; padding: 5px 10px; font-size: 12px; font-weight: bold; border-radius: 4px; display: none; z-index: 20; }
        #status-text { color: #ffcc00; font-weight: bold; margin-bottom: 5px; font-size: 14px; }
        .nav-bar { position: fixed; bottom: 0; left: 0; width: 100%; background: #010101; border-top: 1px solid #2f2f2f; display: flex; justify-content: space-around; align-items: center; padding: 10px 0; z-index: 100; }
        .nav-item { color: #8a8a8a; cursor: pointer; font-size: 12px; display: flex; flex-direction: column; align-items: center; font-weight: bold; }
        .nav-item.active { color: white; }
        .nav-icon { font-size: 20px; margin-bottom: 2px; }
        .plus-btn { background: white; color: black; padding: 3px 12px; border-radius: 8px; font-size: 18px; font-weight: 900; position: relative; box-shadow: -3px 0 #25f4ee, 3px 0 #fe2c55; }
        .sub-page { background: #161618; border: 2px solid #2f2f2f; max-width: 450px; margin: 30px auto; padding: 30px; border-radius: 12px; box-sizing: border-box; }
        .friend-item { text-align: left; padding: 10px; background: #2f2f2f; border-radius: 8px; margin: 10px 0; font-size: 14px; }
        .profile-pic { width: 80px; height: 80px; background: #2f2f2f; border-radius: 50%; margin: 0 auto 15px; display: flex; align-items: center; justify-content: center; font-size: 35px; color: #8a8a8a; border: 3px solid #fe2c55; }
        .stats-container { display: flex; justify-content: center; gap: 20px; margin: 15px 0; }
    </style>
</head>
<body>

    <!-- መለያ ፍጠር (Register) -->
    <div id="registerBox" class="auth-container">
        <h1>መለያ ፍጠር (Register)</h1>
        <p style="color: #8a8a8a;">በስልክ ቁጥርዎ ይመዝገቡ</p>
        <input type="tel" id="regPhone" class="auth-input" placeholder="የስልክ ቁጥር (ምሳሌ፡ 0912...)" required>
        <input type="password" id="regPassword" class="auth-input" placeholder="ሚስጥር ቃል (Password)" required>
        <button class="auth-btn" onclick="handleRegister()">ይመዝገቡ (Register)</button>
        <span class="toggle-link" onclick="showLogin()">አካውንት አለዎት? ይግቡ (Login)</span>
    </div>

    <!-- ይግቡ (Login) -->
    <div id="loginBox" class="auth-container" style="display: none;">
        <h1>ይግቡ (Login)</h1>
        <p style="color: #8a8a8a;">ስልክ ቁጥር እና ሚስጥር ቃል ያስገቡ</p>
        <input type="tel" id="loginPhone" class="auth-input" placeholder="የስልክ ቁጥር">
        <input type="password" id="loginPassword" class="auth-input" placeholder="ሚስጥር ቃል">
        <button class="auth-btn" onclick="handleLogin()">ይግቡ (Login)</button>
        <span class="toggle-link" onclick="showRegister()">አዲስ አካውንት ለመክፈት ይመዝገቡ</span>
    </div>

    <!-- ዋናው ሲስተም -->
    <div id="mainApp">
        <!-- HOME PAGE (Live Stream) -->
        <div id="page-home" class="page-content active">
            <h1>🎵 TikTok Live Active System</h1>
            <div id="status-text">ሲስተሙ ዝግጁ ነው! እባክዎ ላይቭን አክቲቭ ያድርጉ።</div>
            <div style="margin-bottom: 15px;">
                <button id="startLiveBtn" class="auth-btn" style="width: auto; padding: 10px 25px;" onclick="startLive()">🟢 ላይቭ አክቲቭ አድርግ (Activate Live)</button>
            </div>
            <div class="container">
                <div class="video-box">
                    <div id="liveBadge" class="live-badge">LIVE</div>
                    <canvas id="digitalStream"></canvas>
                </div>
                <div id="chat-box">
                    <div id="messages"><p style="color: #8a8a8a;">ስርዓቱ ዝግጁ ነው! ላይቭ ሲገቡ ውይይቱ ይጀምራል።</p></div>
                    <div class="comment-input-area">
                        <input type="text" id="commentInput" class="comment-input" placeholder="ኮሜንት ይጻፉ..." onkeypress="handleKeyPress(event)">
                        <button class="send-btn" onclick="sendComment()">ላክ</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- FRIENDS PAGE -->
        <div id="page-friends" class="page-content">
            <div class="sub-page">
                <h2>👥 ጓደኞች (Friends)</h2>
                <hr style="border-color: #2f2f2f; margin: 15px 0;">
                <p class="friend-item">👤 ተጠቃሚ 0922****** • <span style="color: #fe2c55;">Live አሁን</span></p>
                <p class="friend-item">👤 ተጠቃሚ 0911****** • ከ 2 ሰዓት በፊት</p>
            </div>
        </div>

        <!-- CREATE PAGE -->
        <div id="page-create" class="page-content">
            <div class="sub-page">
                <h2>➕ አዲስ ቪዲዮ መፍጠሪያ</h2>
                <button class="auth-btn" style="width: auto; padding: 12px 30px;">📂 ፋይል ይምረጡ (Upload)</button>
            </div>
        </div>

        <!-- INBOX PAGE -->
        <div id="page-inbox" class="page-content">
            <div class="sub-page">
                <h2>📥 መልዕክቶች (Inbox)</h2>
                <p style="color: #8a8a8a;">ምንም አዲስ መልዕክት የለም።</p>
            </div>
        </div>

        <!-- PROFILE PAGE -->
        <div id="page-profile" class="page-content">
            <div class="sub-page">
                <div class="profile-pic">👤</div>
                <h2 id="profilePhone">0912******</h2>
                <div class="stats-container">
                    <div><strong>1.2K</strong><br><span style="color: #8a8a8a; font-size:13px;">ተከታዮች</span></div>
                    <div><strong>5.4K</strong><br><span style="color: #8a8a8a; font-size:13px;">ላይኮች</span></div>
                </div>
                <button class="auth-btn" style="background: #2f2f2f; color: white;" onclick="location.reload()">🚪 ውጣ (Logout)</button>
            </div>
        </div>

        <!-- NAVIGATION -->
        <div class="nav-bar">
            <div class="nav-item active" id="btn-home" onclick="switchPage('home')"><span class="nav-icon">🏠</span><span>Home</span></div>
            <div class="nav-item" id="btn-friends" onclick="switchPage('friends')"><span class="nav-icon">👥</span><span>Friends</span></div>
            <div class="nav-item" id="btn-create" onclick="switchPage('create')"><span class="plus-btn">+</span></div>
            <div class="nav-item" id="btn-inbox" onclick="switchPage('inbox')"><span class="nav-icon">💬</span><span>Inbox</span></div>
            <div class="nav-item" id="btn-profile" onclick="switchPage('profile')"><span class="nav-icon">👤</span><span>Profile</span></div>
        </div>
    </div>

    <!-- የአሠራር ጃቫስክሪፕት ፋይል ማገናኛ -->
    <script src="script.js"></script>
</body>
</html>
