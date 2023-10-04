<template>
  <div id="UserLayout">
    <a-layout style="min-height: 100vh">
      <a-layout-header class="header">
        <a-space>
          <img src="../assets/logo.jpg" class="logo" />
          <div class="title">OJ 判题系统</div>
        </a-space>
      </a-layout-header>
      <a-layout-content class="content">
        <router-view />
      </a-layout-content>
      <a-layout-footer class="footer">
        <a href="https://yupi.icu" target="_blank">
          编程导航知识星球 by 程序员鱼皮
        </a>
      </a-layout-footer>
    </a-layout>
  </div>
</template>

<style scoped>
#UserLayout {
  background: url("../assets/background.png") 0% 0% / 100% 100%;
  text-align: center;
}

#UserLayout .header {
  margin-top: 16px;
}

#UserLayout .logo {
  width: 64px;
  height: 64px;
}

#UserLayout .title {
  margin-left: 16px;
}

#UserLayout .content {
  margin-bottom: 16px;
  padding: 20px;
}

#UserLayout .footer {
  padding: 16px;
  position: sticky;
  bottom: 0;
  left: 0;
  right: 0;
  text-align: center;
}
</style>
<script></script>
