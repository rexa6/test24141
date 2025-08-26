<template>
  <div v-if="user">
    <div class="profile-header">
      <img :src="user.photo || defaultPhoto" alt="User Avatar" />
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
  <div v-else>
    Загрузка данных...
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      user: null,
      defaultPhoto: "https://via.placeholder.com/100",
      apiUrl: "http://100.79.141.81:8000/users/", // твой сервер
    };
  },
  async mounted() {
    const tg = window.Telegram?.WebApp;
    console.log("initDataUnsafe:", tg?.initDataUnsafe);

    if (tg?.initDataUnsafe?.user) {
      const u = tg.initDataUnsafe.user;

      // собираем данные
      this.user = {
        telegram_id: u.id, // сохраняем как telegram_id
        first_name: u.first_name,
        last_name: u.last_name,
        username: u.username,
        photo: u.photo_url || null,
        balance: 0,
      };

      try {
        // запрос баланса с сервера по telegram_id
        const response = await axios.get(`${this.apiUrl}${this.user.telegram_id}`);
        if (response.data && !response.data.error) {
          this.user.balance = response.data.balance;
        } else {
          console.warn("Юзер не найден на сервере, надо создать");
          // если юзера нет в БД → создаём его
          const createRes = await axios.post(this.apiUrl, {
            telegram_id: this.user.telegram_id,
            username: this.user.username,
            photo: this.user.photo,
          });
          this.user.balance = createRes.data.balance ?? 1000;
        }
      } catch (err) {
        console.error("Ошибка при получении данных с сервера:", err);
      }

      tg.expand();
    } else {
      console.warn("Данные пользователя недоступны");
    }
  },
};
</script>

<style scoped>
.profile-card {
  max-width: 100%;
  width: calc(100% - 40px);
  margin: 20px auto;
  padding: 20px;
  border-radius: 25px;
  background: rgba(40, 40, 40, 0.5);
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
  backdrop-filter: blur(10px);
  color: #b0b0b0;
  font-family: 'Arial', sans-serif;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
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
  object-fit: cover;
  border: 2px solid #b0b0b0;
}

.user-details h2 {
  margin: 0;
  font-size: 1.4rem;
  color: #fff;
  text-shadow: 0 0 8px rgba(168, 85, 247, 0.6);
}

.user-id {
  font-size: 0.9rem;
  opacity: 0.7;
}

.balance-section {
  background: rgba(255, 255, 255, 0.08);
  padding: 12px;
  border-radius: 20px;
  text-align: center;
  box-shadow: inset 0 0 10px rgba(255, 255, 255, 0.2);
  transition: all 0.3s ease;
}

.balance-section h3 {
  margin: 5px 0 0;
  font-size: 1.5rem;
  text-shadow: 0 0 10px rgba(255,255,255,0.6);
}

.loading {
  text-align: center;
  color: #fff;
  margin-top: 50px;
}
</style>
