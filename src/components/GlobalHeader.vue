<template>
  <a-row id="globalHeader" align="center" :wrap="false">
    <a-col flex="auto">
      <a-menu
        mode="horizontal"
        :selected-keys="selectedKeys"
        @menu-item-click="doMenuClick"
      >
        <a-menu-item
          key="0"
          :style="{ padding: 0, marginRight: '38px' }"
          disabled
        >
          <div class="title-bar">
            <img class="logo" src="../assets/logo.jpg" />
            <div class="title">OJ 判题系统</div>
          </div>
        </a-menu-item>
        <a-menu-item v-for="item in visibleRoutes" :key="item.path">
          {{ item.name }}
        </a-menu-item>
      </a-menu>
    </a-col>
    <a-col flex="100px">
      <!--获取状态变量的值，加?给他降级-->
      <div>{{ store.state.user?.loginUser?.userName ?? "未登录" }}</div>
    </a-col>
  </a-row>
</template>

<script setup lang="ts">
import { routes } from "@/router/routes";
import { useRoute, useRouter } from "vue-router";
import { computed, ref } from "vue";
import { useStore } from "vuex";
import checkAccess from "@/access/checkAccess";
import ACCESS_ENUM from "@/access/accessEnum";

const router = useRouter();

//获取状态变量
const store = useStore();
const loginUser = store.state.user.loginUser;
//展示在菜单的路由数组(加了计算属性)
const visibleRoutes = computed(() => {
  return routes.filter((item, index) => {
    if (item.meta?.hideInMenu) {
      return false;
    }
    //根据权限过滤菜单
    if (!checkAccess(loginUser, item?.meta?.access as string)) {
      return false;
    }
    return true;
  });
});
//默认主页
const selectedKeys = ref(["/"]);
//路由跳转后，更新选中的菜单项
router.afterEach((to, from, failure) => {
  selectedKeys.value = [to.path];
});
setTimeout(() => {
  //修改状态变量
  store.dispatch("user/getLoginUser", {
    userName: "张三",
    userRole: ACCESS_ENUM.ADMIN,
  });
}, 3000);
const doMenuClick = (key: string) => {
  router.push({
    path: key,
  });
};
</script>

<style scoped>
.title-bar {
  display: flex;
  align-items: center;
}

.title {
  color: #444;
  margin-left: 16px;
}

.logo {
  height: 48px;
  width: 48px;
}
</style>
