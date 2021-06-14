<template>
  <div class="locale-switcher">
    <select v-model="$i18n.locale">
      <option value="en">English</option>
      <option value="fr">Français</option>
      <option value="de">Deutsche</option>
      <option value="pt">Português</option>
      <option value="zh">中文</option>
    </select>
  </div>
</template>