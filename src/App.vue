<script setup>
import { ref } from "vue";
import BottomNav from "./components/BottomNav.vue";

import Upgrade from "./components/Upgrade.vue";
import Battle from "./components/Battle.vue";
import Profile from "./components/Profile.vue";

const activeTab = ref("Upgrade");

const componentsMap = {
  Upgrade,
  Battle,
  Profile
};
</script>

<template>
  <div class="app">
    <!-- Контент -->
    <component :is="componentsMap[activeTab]" />

    <!-- Нижний таб-бар -->
    <BottomNav v-model:activeTab="activeTab" />
  </div>
</template>

<style>
.app {
  min-height: 100vh;
  color: #fff;
  padding-bottom: 120px;
}
</style>
