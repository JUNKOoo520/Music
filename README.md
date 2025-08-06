<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>我的音乐播放器</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: white;
            margin: 0;
            padding: 0;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .player-container {
            width: 90%;
            max-width: 400px;
            background: rgba(0, 0, 0, 0.7);
            border-radius: 20px;
            padding: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
        }
        
        .album-art {
            width: 200px;
            height: 200px;
            margin: 0 auto 20px;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.3);
        }
        
        .album-art img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .song-info {
            text-align: center;
            margin-bottom: 20px;
        }
        
        .song-title {
            font-size: 22px;
            font-weight: bold;
            margin-bottom: 5px;
        }
        
        .song-artist {
            font-size: 16px;
            color: #aaa;
        }
        
        .progress-container {
            width: 100%;
            height: 6px;
            background: rgba(255, 255, 255, 0.2);
            border-radius: 3px;
            margin-bottom: 20px;
            cursor: pointer;
        }
        
        .progress-bar {
            height: 100%;
            background: #1db954;
            border-radius: 3px;
            width: 0%;
            transition: width 0.1s linear;
        }
        
        .time-info {
            display: flex;
            justify-content: space-between;
            font-size: 12px;
            color: #aaa;
            margin-bottom: 20px;
        }
        
        .controls {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 20px;
        }
        
        .control-btn {
            background: none;
            border: none;
            color: white;
            font-size: 24px;
            margin: 0 15px;
            cursor: pointer;
            transition: transform 0.2s;
        }
        
        .control-btn:hover {
            transform: scale(1.1);
        }
        
        .play-btn {
            font-size: 36px;
            background: #1db954;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        
        .playlist {
            max-height: 200px;
            overflow-y: auto;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            padding-top: 10px;
        }
        
        .playlist-item {
            padding: 10px;
            border-radius: 5px;
            margin-bottom: 5px;
            cursor: pointer;
            transition: background 0.2s;
        }
        
        .playlist-item:hover {
            background: rgba(255, 255, 255, 0.1);
        }
        
        .playlist-item.active {
            background: rgba(29, 185, 84, 0.3);
            color: #1db954;
        }
        
        /* 滚动条样式 */
        .playlist::-webkit-scrollbar {
            width: 5px;
        }
        
        .playlist::-webkit-scrollbar-track {
            background: rgba(255, 255, 255, 0.1);
        }
        
        .playlist::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.3);
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div class="player-container">
        <div class="album-art">
            <img id="albumCover" src="https://via.placeholder.com/200" alt="专辑封面">
        </div>
        
        <div class="song-info">
            <div class="song-title" id="songTitle">歌曲名称</div>
            <div class="song-artist" id="songArtist">歌手名称</div>
        </div>
        
        <div class="progress-container" id="progressContainer">
            <div class="progress-bar" id="progressBar"></div>
        </div>
        
        <div class="time-info">
            <span id="currentTime">0:00</span>
            <span id="duration">0:00</span>
        </div>
        
        <div class="controls">
            <button class="control-btn" id="prevBtn">⏮</button>
            <button class="control-btn play-btn" id="playBtn">▶</button>
            <button class="control-btn" id="nextBtn">⏭</button>
        </div>
        
        <div class="playlist" id="playlist">
            <!-- 播放列表将通过JavaScript动态生成 -->
        </div>
    </div>

    <script>
        // 音乐列表数据
        const songs = [
            {
                title: "示例歌曲1",
                artist: "示例歌手",
                src: "https://share.weiyun.com/99ohpV0Y",
                cover: "https://via.placeholder.com/200"
            },
            {
                title: "示例歌曲2",
                artist: "示例歌手",
                src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-2.mp3",
                cover: "https://via.placeholder.com/200"
            },
            {
                title: "示例歌曲3",
                artist: "示例歌手",
                src: "https://www.soundhelix.com/examples/mp3/SoundHelix-Song-3.mp3",
                cover: "https://via.placeholder.com/200"
            }
        ];
        
        // 获取DOM元素
        const audio = new Audio();
        const albumCover = document.getElementById('albumCover');
        const songTitle = document.getElementById('songTitle');
        const songArtist = document.getElementById('songArtist');
        const progressBar = document.getElementById('progressBar');
        const progressContainer = document.getElementById('progressContainer');
        const currentTimeEl = document.getElementById('currentTime');
        const durationEl = document.getElementById('duration');
        const playBtn = document.getElementById('playBtn');
        const prevBtn = document.getElementById('prevBtn');
        const nextBtn = document.getElementById('nextBtn');
        const playlistEl = document.getElementById('playlist');
        
        // 当前歌曲索引
        let currentSongIndex = 0;
        let isPlaying = false;
        
        // 初始化播放器
        function initPlayer() {
            // 生成播放列表
            renderPlaylist();
            
            // 加载第一首歌
            loadSong(currentSongIndex);
            
            // 事件监听
            playBtn.addEventListener('click', togglePlay);
            prevBtn.addEventListener('click', prevSong);
            nextBtn.addEventListener('click', nextSong);
            progressContainer.addEventListener('click', setProgress);
            audio.addEventListener('timeupdate', updateProgress);
            audio.addEventListener('ended', nextSong);
            audio.addEventListener('loadedmetadata', updateSongInfo);
        }
        
        // 渲染播放列表
        function renderPlaylist() {
            playlistEl.innerHTML = '';
            songs.forEach((song, index) => {
                const songEl = document.createElement('div');
                songEl.classList.add('playlist-item');
                if (index === currentSongIndex) {
                    songEl.classList.add('active');
                }
                songEl.innerHTML = `
                    <div>${song.title}</div>
                    <div style="font-size:12px;color:#aaa;">${song.artist}</div>
                `;
                songEl.addEventListener('click', () => playSong(index));
                playlistEl.appendChild(songEl);
            });
        }
        
        // 加载歌曲
        function loadSong(index) {
            const song = songs[index];
            audio.src = song.src;
            albumCover.src = song.cover;
            songTitle.textContent = song.title;
            songArtist.textContent = song.artist;
            
            // 更新播放列表高亮
            const playlistItems = document.querySelectorAll('.playlist-item');
            playlistItems.forEach(item => item.classList.remove('active'));
            playlistItems[index].classList.add('active');
        }
        
        // 播放歌曲
        function playSong(index) {
            if (index !== currentSongIndex) {
                currentSongIndex = index;
                loadSong(currentSongIndex);
            }
            audio.play();
            isPlaying = true;
            playBtn.innerHTML = '⏸';
        }
        
        // 暂停/播放切换
        function togglePlay() {
            if (isPlaying) {
                audio.pause();
                playBtn.innerHTML = '▶';
            } else {
                audio.play();
                playBtn.innerHTML = '⏸';
            }
            isPlaying = !isPlaying;
        }
        
        // 上一首
        function prevSong() {
            currentSongIndex--;
            if (currentSongIndex < 0) {
                currentSongIndex = songs.length - 1;
            }
            playSong(currentSongIndex);
        }
        
        // 下一首
        function nextSong() {
            currentSongIndex++;
            if (currentSongIndex > songs.length - 1) {
                currentSongIndex = 0;
            }
            playSong(currentSongIndex);
        }
        
        // 更新进度条
        function updateProgress() {
            const { currentTime, duration } = audio;
            const progressPercent = (currentTime / duration) * 100;
            progressBar.style.width = `${progressPercent}%`;
            
            // 更新时间显示
            currentTimeEl.textContent = formatTime(currentTime);
            if (duration) {
                durationEl.textContent = formatTime(duration);
            }
        }
        
        // 设置进度
        function setProgress(e) {
            const width = this.clientWidth;
            const clickX = e.offsetX;
            const duration = audio.duration;
            audio.currentTime = (clickX / width) * duration;
        }
        
        // 更新歌曲信息
        function updateSongInfo() {
            durationEl.textContent = formatTime(audio.duration);
        }
        
        // 格式化时间
        function formatTime(seconds) {
            const mins = Math.floor(seconds / 60);
            const secs = Math.floor(seconds % 60);
            return `${mins}:${secs < 10 ? '0' : ''}${secs}`;
        }
        
        // 初始化播放器
        initPlayer();
    </script>
</body>
</html>
