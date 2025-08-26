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

      try {
        // сначала пробуем достать юзера с сервера
        let response = await axios.get(`${this.apiUrl}${u.id}`);

        if (response.data && !response.data.error) {
          // ✅ сервер вернул юзера → используем его целиком
          this.user = response.data;
        } else {
          // ❌ нет такого юзера → создаём
          console.warn("Юзер не найден на сервере, создаём...");
          const createRes = await axios.post(this.apiUrl, {
            telegram_id: u.id,
            username: u.username,
            photo: u.photo_url || null,
          });
          this.user = createRes.data; // ✅ сразу юзер с балансом
        }
      } catch (err) {
        console.error("Ошибка при запросе к серверу:", err);
      }

      tg.expand();
    } else {
      console.warn("Данные пользователя недоступны");
    }
  },
};
</script>


<style scoped>
/* Общий фон */
:host {
  display: block;
  font-family: 'Inter', 'Arial', sans-serif;
  background: linear-gradient(145deg, #1c1c1e, #2c2c2e);
  min-height: 100vh;
  padding: 20px;
  box-sizing: border-box;
  color: #fff;
}

/* Карточка профиля */
.profile-card {
  width: 100%;
  max-width: 500px;
  margin: 0 auto;
  padding: 22px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.05);
  box-shadow: 0 8px 25px rgba(0,0,0,0.6);
  backdrop-filter: blur(14px);
  transition: transform 0.3s ease;
}
.profile-card:hover {
  transform: scale(1.02);
}

/* Хедер с аватаркой */
.profile-header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 20px;
}

.profile-header img {
  width: 90px;
  height: 90px;
  border-radius: 50%;
  object-fit: cover;
  border: 3px solid rgba(255,255,255,0.3);
  box-shadow: 0 0 15px rgba(0,0,0,0.5);
  transition: all 0.3s ease;
}
.profile-header img:hover {
  border-color: #a855f7;
  box-shadow: 0 0 20px rgba(168,85,247,0.8);
}

/* Имя и айди */
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
  margin: 4px 0 0;
}

/* Баланс */
.balance-section {
  margin-top: 15px;
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
  margin: 6px 0 0;
  font-size: 1.8rem;
  font-weight: 700;
  color: #fff;
  text-shadow: 0 0 12px rgba(168,85,247,0.8);
}

/* Загрузка */
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
