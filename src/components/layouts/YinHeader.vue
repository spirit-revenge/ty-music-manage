<template>
  <div class="header">
    <div class="collapse-btn" @click="collapseChage">
      <el-icon v-if="!collapse"><expand /></el-icon>
      <el-icon v-else><fold /></el-icon>
    </div>
    <div class="logo">{{ nusicName }}</div>
    <div class="header-right">
      <div class="header-user-con">
        <el-dropdown class="user-name" trigger="click" @command="handleCommand">
          <div class="user-avator">
            <el-avatar v-if="userPic" :src="attachImageUrl(userPic)"></el-avatar>
            <el-avatar v-if="!userPic" style="background: darkred">{{username.substring(0,1).toUpperCase()}}</el-avatar>
<!--            <img :src="attachImageUrl(userPic)" />-->
          </div>
<!--          <span class="el-dropdown-link">-->
<!--            {{ username }}-->
<!--            <i class="el-icon-caret-bottom"></i>-->
<!--          </span>-->
          <template #dropdown>
            <el-dropdown-menu>
<!--              <span class="el-dropdown-link">{{username}}</span>-->
              <el-dropdown-item>{{username}}</el-dropdown-item>
              <el-dropdown-item command="loginout">退出登录</el-dropdown-item>
            </el-dropdown-menu>
          </template>
        </el-dropdown>
      </div>
    </div>
  </div>
</template>
<script lang="ts">
import { defineComponent, computed, ref, onMounted } from "vue";
import { useStore } from "vuex";
import mixin from "@/mixins/mixin";
import { Expand, Fold } from "@element-plus/icons-vue";
import emitter from "@/utils/emitter";
import { HttpManager } from "@/api";
import { RouterName, MUSICNAME } from "@/enums";

export default defineComponent({
  components: {
    Expand,
    Fold,
  },
  setup() {
    const { routerManager } = mixin();
    const store = useStore();

    const collapse = ref(true);
    const username = ref("admin");
    const userPic = computed(() => store.getters.userPic);
    const nusicName = ref(MUSICNAME);

    onMounted(() => {
      if (document.body.clientWidth < 1500) {
        collapseChage();
      }
    });

    // 侧边栏折叠
    function collapseChage() {
      collapse.value = !collapse.value;
      emitter.emit("collapse", collapse.value);
    }
    // 用户名下拉菜单选择事件
    function handleCommand(command) {
      if (command === "loginout") {
        routerManager(RouterName.SignIn, { path: RouterName.SignIn });
      }
    }
    return {
      nusicName,
      username,
      userPic,
      collapse,
      collapseChage,
      handleCommand,
      attachImageUrl: HttpManager.attachImageUrl,
    };
  },
});
</script>
<style scoped>
.header {
  position: absolute;
  z-index: 100;
  width: 100%;
  height: 60px;
  display: flex;
  align-items: center;
  font-size: 20px;
  color: #2c3e50;
  background: #EC4141;
  box-shadow: 0px 0px 8px 2px rgba(0, 0, 0, 0.3);
}

.collapse-btn {
  display: flex;
  padding: 0 21px;
  cursor: pointer;
}

.header .logo {
  width: 250px;
  font-weight: bold;
}

.header-right {
  position: absolute;
  right: 0;
  padding-right: 30px;
}

.header-user-con {
  display: flex;
  align-items: center;
}

.user-name {
  margin-left: 10px;
}

.user-avator img {
  display: block;
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.el-dropdown-link {
  cursor: pointer;
}

.el-dropdown-menu__item {
  text-align: center;
}
</style>
