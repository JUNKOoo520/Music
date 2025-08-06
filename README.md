# Music
<!DOCTYPE html>
<html>
<head>
    <title>我的音乐播放器</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
            background: #f0f0f0;
            padding: 20px;
        }
        .player {
            background: white;
            border-radius: 10px;
            padding: 20px;
            max-width: 400px;
            margin: 0 auto;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        audio {
            width: 100%;
            margin: 15px 0;
        }
        h1 {
            color: #333;
        }
    </style>
</head>
<body>
    <div class="player">
        <h1>我的音乐播放器</h1>
        <audio controls autoplay>
            <source src="YOUR_MUSIC_LINK" type="audio/mp3">
            你的浏览器不支持音频播放。
        </audio>
        <p>点击播放按钮即可播放音乐</p>
    </div>
</body>
</html>
