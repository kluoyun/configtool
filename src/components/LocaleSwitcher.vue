<template>
  <div class="locale-switcher">
    <select v-model="$i18n.locale">
      <option value="en">English</option>
      <option value="zh">中文</option>
      <option value="pt">Português</option> 
    </select>
  </div>
</template>