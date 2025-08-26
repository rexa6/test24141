<template>
  <div class="profile-card">
    <div class="profile-header">
      <img :src="user.photo" alt="User Avatar" class="avatar">
      <div class="user-details">
        <h2>{{ user.username }}</h2>
        <p class="user-id">ID: {{ user.telegram_id }}</p>
      </div>
    </div>

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
        telegram_id: "",
        username: "",
        balance: 0
      },
      apiUrl: "http://127.0.0.1:8000/users/"
    };
  },
  methods: {
    async fetchUser() {
      if (!this.telegram_id) return;
      try {
        const response = await axios.get(`${this.apiUrl}${this.telegram_id}`);
        const data = response.data;
        console.log("Fetched user:", data); // чтобы видеть данные в консоли
        this.user = {
          telegram_id: data.telegram_id,
          username: data.username,
          balance: data.balance,
          photo: data.photo || `https://avatars.dicebear.com/api/bottts/${data.telegram_id}.png`
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
  padding: 25px;
  border-radius: 25px;
  background: linear-gradient(135deg, #4b0082, #8a2be2);
  box-shadow: 0 12px 25px rgba(0,0,0,0.35);
  color: #fff;
  font-family: 'Arial', sans-serif;
  transition: all 0.3s ease;
}
.profile-card:hover { transform: translateY(-5px); }
.profile-header { display: flex; align-items: center; gap: 20px; margin-bottom: 25px; }
.avatar { width: 90px; height: 90px; border-radius: 50%; border: 3px solid rgba(255,255,255,0.8); object-fit: cover; }
.user-details h2 { margin: 0; font-size: 1.6rem; text-shadow: 0 0 8px rgba(168,85,247,0.6); }
.user-id { font-size: 0.9rem; opacity: 0.7; }
.balance-section { text-align: center; background: rgba(255,255,255,0.08); padding: 15px; border-radius: 20px; box-shadow: inset 0 0 10px rgba(255,255,255,0.2); }
.balance-section h3 { margin: 5px 0 0; font-size: 1.5rem; text-shadow: 0 0 10px rgba(255,255,255,0.6); }
</style>
