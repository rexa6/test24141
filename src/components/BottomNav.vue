<script setup>
import { ref } from "vue";

const props = defineProps({
  activeTab: String
});
const emit = defineEmits(["update:activeTab"]);

// Пути к SVG в папке public
const tabs = [
  { name: "Battle", icon: "/battle.svg" },
  { name: "Upgrade", icon: "/upgrade.svg" },
  { name: "Profile", icon: "/profile.svg" }
];

function setTab(tabName) {
  emit("update:activeTab", tabName);
}
</script>

<template>
  <nav class="nav">
    <ul>
      <li
        v-for="tab in tabs"
        :key="tab.name"
        @click="setTab(tab.name)"
        :class="{ active: tab.name === activeTab }"
      >
        <span class="icon">
          <img :src="tab.icon" :alt="tab.name" />
        </span>
        <span class="label">{{ tab.name }}</span>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.nav {
  position: fixed;
  bottom: 20px;
  left: 20px;
  right: 20px;
  background: rgba(40, 40, 40, 0.5);
  border-radius: 25px;
  padding: 12px;
  display: flex;
  justify-content: center;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
  backdrop-filter: blur(10px);
  z-index: 10;
  transition: all 0.5s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.nav ul {
  display: flex;
  list-style: none;
  margin: 0;
  padding: 0;
  width: 100%;
  justify-content: space-between;
}

.nav li {
  flex: 1;
  margin: 0 5px;
  text-align: center;
  padding: 12px 0;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  font-weight: 600;
  font-size: 14px;
  color: #b0b0b0;
  border-radius: 20px;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  z-index: 1;
}

.nav li:hover {
  color: #b0b0b0;
  background: rgba(255, 255, 255, 0.08);
  z-index: 2;
}

.nav li.active {
  background: linear-gradient(135deg, #3a3a3c, #2a2a2c);
  color: #b0b0b0;
  box-shadow: 0 6px 15px rgba(0, 0, 0, 0.35);
  z-index: 3;
}

.nav .icon {
  font-size: 24px;
  margin-bottom: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.nav .icon img {
  width: 28px;
  height: 28px;
  filter: brightness(1.5);
  transition: transform 0.5s cubic-bezier(0.25, 0.8, 0.25, 1), filter 0.5s ease;
}

.nav li:hover .icon img,
.nav li.active .icon img {
  transform: translateY(-2px);
  filter: brightness(1.8);
}

.nav .label {
  transition: all 0.5s cubic-bezier(0.25, 0.8, 0.25, 1);
}

.nav li.active .label {
  font-weight: 700;
  color: #b0b0b0;
  text-shadow: 
    0 0 8px rgba(168, 85, 247, 0.6),
    0 0 16px rgba(168, 85, 247, 0.4),
    0 0 24px rgba(168, 85, 247, 0.2);
}
</style>
