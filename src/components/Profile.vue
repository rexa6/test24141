<template>
  <div class="profile-card">
    <!-- Аватар и никнейм -->
    <div class="profile-header">
      <img :src="user.photo" alt="User Avatar" class="avatar">
      <div class="user-details">
        <h2>{{ user.username }}</h2>
        <p class="user-id">ID: {{ user.id }}</p>
      </div>
    </div>

    <!-- Баланс -->
    <div class="balance-section">
      <p>💰 Баланс</p>
      <h3>{{ user.balance }} TON</h3>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      telegram_id: null,
      user: {
        photo: "",
        id: "",
        username: "",
        balance: 0
      },
      apiUrl: "http://100.79.141.81:8000/users/"
    };
  },
  methods: {
    async fetchUser() {
      if (!this.telegram_id) return;
      try {
        const response = await axios.get(`${this.apiUrl}${this.telegram_id}`);
        const data = response.data;
        this.user = {
          id: data.id,
          username: data.username,
          balance: data.balance,
          photo: data.photo || `https://avatars.dicebear.com/api/bottts/${data.id}.png`
        };
      } catch (error) {
        console.error("Ошибка при получении данных юзера:", error);
      }
    }
  },
  mounted() {
    if (window.Telegram && window.Telegram.WebApp) {
      const tgUser = window.Telegram.WebApp.initDataUnsafe.user;
      if (tgUser) {
        this.telegram_id = tgUser.id;
        this.user = {
          id: tgUser.id,
          username: tgUser.username || `user_${tgUser.id}`,
          photo: `https://avatars.dicebear.com/api/bottts/${tgUser.id}.png`,
          balance: 0
        };
        this.fetchUser();
      }
    } else {
      console.warn("Это не WebApp Telegram, используем тестовый telegram_id");
      this.telegram_id = 132412215;
      this.fetchUser();
    }
  }
};
</script>

<style scoped>
.profile-card {
  max-width: 350px;
  margin: 40px auto;
  background: linear-gradient(135deg, #4b0082, #8a2be2);
  border-radius: 20px;
  color: white;
  padding: 20px;
  box-shadow: 0 10px 25px rgba(0,0,0,0.3);
  font-family: 'Arial', sans-serif;
}

.profile-header {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 20px;
}

.avatar {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  border: 3px solid white;
  object-fit: cover;
}

.user-details h2 {
  margin: 0;
  font-size: 1.5rem;
}

.user-id {
  margin: 5px 0 0;
  font-size: 0.9rem;
  opacity: 0.8;
}

.balance-section {
  background: rgba(255,255,255,0.1);
  border-radius: 15px;
  padding: 15px;
  text-align: center;
}

.balance-section p {
  margin: 0;
  font-size: 1rem;
  opacity: 0.8;
}

.balance-section h3 {
  margin: 5px 0 0;
  font-size: 1.5rem;
}
</style>
