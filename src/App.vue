<template>
  <div class="geo-login-video">
    <video v-if="locationGranted" autoplay loop muted class="background-video">
      <source src="/geo-location-background.mp4" type="video/mp4" />
      Your browser does not support HTML5 video.
    </video>

    <div class="login-container">
      <div v-if="showLogin" class="login-form">
        <img src="/instagram-logo.png" alt="Instagram" class="instagram-logo" />
        <h2 class="instagram-text">Instagram</h2>
        <input
          type="text"
          placeholder="Phone number, username, or email"
          v-model="username"
        />
        <input type="password" placeholder="Password" v-model="password" />
        <button class="login-btn" @click="submitLogin">Log in</button>
        <div class="or-divider">
          <span class="divider-line"></span>
          <span class="or-text">OR</span>
          <span class="divider-line"></span>
        </div>
        <a href="#" class="forgot-password">Forgot password?</a>
        <div class="signup-container">
          <p>
            Don't have an account? <a href="#" class="signup-link">Sign up</a>
          </p>
        </div>
      </div>
      <div v-else class="location-request">
        <h1>Welcome!</h1>
        <p>Please enable location services to continue.</p>
        <button @click="getLocation">Enable Location</button>
        <p v-if="locationError" class="error-message">
          Error: {{ locationError }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      showLogin: false,
      locationGranted: false,
      username: "",
      password: "",
      locationError: null,
    };
  },
  methods: {
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          this.handleLocationSuccess,
          this.handleLocationError,
          {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0,
          }
        );
      } else {
        this.locationError = "Geolocation is not supported by this browser.";
      }
    },
    handleLocationSuccess(position) {
      const latitude = position.coords.latitude;
      const longitude = position.coords.longitude;
      console.log("Latitude: " + latitude + ", Longitude: " + longitude);
      this.locationGranted = true;
      this.showLogin = true;
      this.locationError = null;
    },
    handleLocationError(error) {
      this.locationGranted = false;
      this.showLogin = false;
      switch (error.code) {
        case error.PERMISSION_DENIED:
          this.locationError = "User denied the request for Geolocation.";
          break;
        case error.POSITION_UNAVAILABLE:
          this.locationError = "Location information is unavailable.";
          break;
        case error.TIMEOUT:
          this.locationError = "The request to get user location timed out.";
          break;
        case error.UNKNOWN_ERROR:
          this.locationError = "An unknown error occurred.";
          break;
      }
    },
    submitLogin() {
      console.log("Logging in with:", this.username, this.password);
    },
  },
};
</script>

<style scoped>
.geo-login-video {
  position: relative;
  width: 100%;
  height: 100vh;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
}

.background-video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
  z-index: -1;
}

.login-container {
  background: rgba(0, 0, 0, 0.5);
  padding: 20px;
  border-radius: 10px;
  color: white;
  text-align: center;
}

.location-request {
  /* Styles for the location request section */
}

.location-request button {
  padding: 10px 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.location-request button:hover {
  background-color: #0056b3;
}

.error-message {
  color: red;
  margin-top: 10px;
}

.login-form {
  background: #000;
  padding: 30px 40px;
  border-radius: 1px;
  width: 350px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  align-items: center;
  text-align: center;
}

.instagram-logo {
  width: 75px;
  height: 75px;
  margin-bottom: 10px;
}

.instagram-text {
  font-family: "Brush Script MT", cursive;
  font-size: 40px;
  margin: 0 0 20px 0;
  font-weight: normal;
}

.login-form input {
  width: 100%;
  padding: 12px 10px;
  border: 1px solid #262626;
  border-radius: 3px;
  background: #121212;
  color: #fff;
  margin-bottom: 6px;
  font-size: 12px;
}

.login-btn {
  background: #0095f6;
  color: #fff;
  border: none;
  border-radius: 4px;
  padding: 8px 0;
  font-weight: bold;
  cursor: pointer;
  width: 100%;
  margin-top: 8px;
}

.login-btn:hover {
  background: #0078cc;
}

.or-divider {
  display: flex;
  align-items: center;
  width: 100%;
  margin: 15px 0;
}

.divider-line {
  flex: 1;
  height: 1px;
  background-color: #262626;
}

.or-text {
  padding: 0 15px;
  color: #8e8e8e;
  font-size: 13px;
  font-weight: 600;
}

.forgot-password {
  color: #0095f6;
  font-size: 12px;
  text-decoration: none;
  margin-top: 5px;
}

.signup-container {
  margin-top: 20px;
  font-size: 14px;
}

.signup-link {
  color: #0095f6;
  text-decoration: none;
  font-weight: 600;
}
</style>
