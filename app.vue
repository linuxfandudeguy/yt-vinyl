<template>
  <div class="container text-center my-5">
    <h1 class="display-4 mb-4">Vinyl Player with YouTube Playlist</h1>

    <!-- YouTube Playlist Input -->
    <div class="input-group mb-4">
      <input 
        type="url" 
        v-model="playlistUrl" 
        class="form-control" 
        placeholder="Enter YouTube Playlist URL"
      >
      <button @click="loadPlaylist" class="btn btn-dark">Load Playlist</button>
    </div>

    <!-- Vinyl Player -->
    <div class="vinyl-container">
      <!-- Core (separate from the vinyl but looks like part of it) -->
      <div class="core" id="core"></div> 

      <div class="vinyl" id="vinyl">
        <div class="video-player" id="video-player">
          <!-- Video will be embedded here once the playlist is loaded -->
        </div>
      </div>

      <!-- Separate arm (needle) fixed in place outside of vinyl rotation -->
      <div class="needle" id="needle"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      playlistUrl: "", // Default value
      videoIframe: null,
    };
  },
  mounted() {
    // Load the YouTube Iframe API script dynamically when the component is mounted
    const script = document.createElement('script');
    script.src = "https://www.youtube.com/iframe_api";
    document.body.appendChild(script);
  },
  methods: {
    loadPlaylist() {
      const playlistId = this.playlistUrl.split('list=')[1];

      if (playlistId) {
        // Embed the first video in the playlist
        this.videoIframe = document.createElement('iframe');
        this.videoIframe.width = '100%';
        this.videoIframe.height = '100%';
        this.videoIframe.src = `https://www.youtube.com/embed?listType=playlist&list=${playlistId}&autoplay=1`;
        this.videoIframe.frameborder = '0';
        this.videoIframe.allow = 'accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture';
        this.videoIframe.allowFullscreen = true;

        // Clear any existing content and add the new iframe
        const videoPlayer = document.getElementById('video-player');
        videoPlayer.innerHTML = '';
        videoPlayer.appendChild(this.videoIframe);

        // Start spinning the vinyl and make it visible
        const vinyl = document.getElementById('vinyl');
        vinyl.style.visibility = 'visible'; // Show vinyl
        vinyl.style.animation = 'spin 10s linear infinite'; // Start spinning animation
      } else {
        alert('Please enter a valid YouTube playlist URL');
      }
    }
  }
}
</script>

<style>
@import './globals.css';

/* Styling for the vinyl container */
.vinyl-container {
  position: relative;
  width: 300px;
  height: 300px;
  margin: 0 auto;
  background-color: #333;
  border-radius: 50%;
  overflow: hidden;
}

/* Vinyl is spinning */
.vinyl {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 50%;
  background: radial-gradient(circle, #000, #333);
  animation: spin 10s linear infinite; /* Only vinyl spins */
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  visibility: hidden; /* Initially hidden */
}

/* Core (separate from spinning vinyl, but looks like part of it) */
.core {
  position: absolute;
  width: 550px; /* MASSIVE size */
  height: 550px; /* MASSIVE size */
  background: url('/core.png') no-repeat center center;
  background-size: contain;
  z-index: 3; /* Higher z-index to sit above vinyl */
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%); /* Center the core exactly in the middle */
  pointer-events: none; /* Prevent the core from interfering with interactions */
}

/* Arm positioned separately from the vinyl */
.needle {
  position: absolute;
  width: 350px;
  height: 250px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
  background: url('/arm.png') no-repeat center center;
  background-size: contain;
  z-index: 2;
  transform-origin: 100% 50%;
  bottom: 10px;
}

/* Vinyl spin animation */
@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

/* Video player inside the vinyl */
.video-player {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 80%;
  height: 80%;
  transform: translate(-50%, -50%);
  overflow: hidden;
  border-radius: 50%;
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.4);
  z-index: 1;
}

.input-group, .btn {
  width: 80%;
  margin: 0 auto;
}
</style>
