<template>
  <div v-if="user">
    <div class="profile-header">
      <img :src="user.photo || defaultPhoto" />
      <div class="user-details">
        <h2>{{ user.username || (user.first_name + ' ' + user.last_name) }}</h2>
        <p>ID: {{ user.telegram_id }}</p>
      </div>
    </div>

    <div class="balance-section">
      <p>💰 Баланс</p>
      <h3>{{ user.balance }} TON</h3>
    </div>
  </div>

  <div v-else>Загрузка данных...</div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      user: null,
      defaultPhoto: "https://via.placeholder.com/100",
      apiUrl: "http://100.79.141.81:8000/users/"
    };
  },
  async mounted() {
    const tg = window.Telegram?.WebApp;
    if (!tg?.initDataUnsafe?.user) return;

    const u = tg.initDataUnsafe.user;

    try {
      // Отправляем POST, чтобы сервер создал или вернул баланс
      const response = await axios.post(this.apiUrl, {
        telegram_id: u.id
      });

      // Формируем объект пользователя для фронта
      this.user = {
        telegram_id: response.data.telegram_id,
        balance: response.data.balance,
        first_name: u.first_name,
        last_name: u.last_name,
        username: u.username,
        photo: u.photo_url
      };

      tg.expand();
    } catch (err) {
      console.error("Ошибка при запросе к серверу:", err);
    }
  }
};
</script>


<style scoped>
:host {
  display: block;
  font-family: 'Inter', 'Arial', sans-serif;
  background: linear-gradient(145deg, #1c1c1e, #2c2c2e);
  min-height: 100vh;
  padding: 20px;
  box-sizing: border-box;
  color: #fff;
}

.profile-card {
  width: 100%;
  max-width: 500px;
  margin: 30px auto;
  padding: 24px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.05);
  box-shadow: 0 8px 25px rgba(0,0,0,0.6);
  backdrop-filter: blur(14px);
  transition: transform 0.3s ease;
}
.profile-card:hover {
  transform: scale(1.02);
}

.profile-header {
  display: flex;
  align-items: center;
  gap: 18px;
  margin-bottom: 25px;
}

.avatar {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid rgba(255,255,255,0.3);
  box-shadow: 0 0 15px rgba(0,0,0,0.5);
  transition: all 0.3s ease;
}
.avatar:hover {
  border-color: #a855f7;
  box-shadow: 0 0 20px rgba(168,85,247,0.8);
}

.user-details h2 {
  margin: 0;
  font-size: 1.6rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 0 0 8px rgba(168, 85, 247, 0.6);
}
.user-details p {
  font-size: 0.9rem;
  opacity: 0.8;
  margin-top: 4px;
}

.balance-section {
  margin-top: 20px;
  padding: 16px;
  border-radius: 18px;
  background: linear-gradient(135deg, rgba(168,85,247,0.15), rgba(255,255,255,0.05));
  text-align: center;
  box-shadow: inset 0 0 10px rgba(168,85,247,0.3);
  transition: all 0.3s ease;
}
.balance-section:hover {
  box-shadow: inset 0 0 18px rgba(168,85,247,0.6);
}

.balance-section p {
  margin: 0;
  font-size: 1rem;
  opacity: 0.85;
  letter-spacing: 0.5px;
}

.balance-section h3 {
  margin-top: 6px;
  font-size: 1.8rem;
  font-weight: 700;
  color: #fff;
  text-shadow: 0 0 12px rgba(168,85,247,0.8);
}

.loading {
  text-align: center;
  color: #aaa;
  margin-top: 50px;
  font-size: 1.2rem;
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% { opacity: 0.5; }
  50% { opacity: 1; }
  100% { opacity: 0.5; }
}
</style>
