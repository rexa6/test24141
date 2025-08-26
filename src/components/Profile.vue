<script>
import axios from "axios";

export default {
  data() {
    return {
      telegram_id: null,
      user: null,
      apiUrl: "http://100.79.141.81:8000/users/"
    };
  },
  methods: {
    async fetchUser() {
      if (!this.telegram_id) return;
      try {
        const response = await axios.get(`${this.apiUrl}${this.telegram_id}`);
        this.user = response.data;
      } catch (error) {
        console.error("Ошибка при получении данных юзера:", error);
      }
    }
  },
  mounted() {
    // Читаем tg_id из query string
    const params = new URLSearchParams(window.location.search);
    const tg_id = params.get("tg_id");
    if (tg_id) {
      this.telegram_id = tg_id;
      this.fetchUser();
    } else if (window.Telegram?.WebApp?.initDataUnsafe?.user) {
      // fallback на initDataUnsafe
      const tgUser = window.Telegram.WebApp.initDataUnsafe.user;
      this.telegram_id = tgUser.id;
      this.fetchUser();
    } else {
      console.warn("Не WebApp Telegram, тестовый ID");
      this.telegram_id = 132412215;
      this.fetchUser();
    }
  }
};
</script>


<style scoped>
.profile-card {
  max-width: 100%;             /* Растягиваем по ширине как nav */
  width: calc(100% - 40px);    /* Отступы слева и справа */
  margin: 20px auto;           /* Центрирование */
  padding: 20px;
  border-radius: 25px;         /* Как у nav */
  background: rgba(40, 40, 40, 0.5); /* Схожий фон с навигацией */
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
</style>
