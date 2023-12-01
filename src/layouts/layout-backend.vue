<template>
  <div class="layout">
    <Layout>
      <Sider
        ref="side1"
        hide-trigger
        collapsible
        :collapsed-width="78"
        v-model="isCollapsed"
        class="layout-sidebar"
        :class="{ 'layout-sidebar-show': showMobileSidebar }"
      >
        <Menu
          active-name="1-2"
          theme="dark"
          width="auto"
          :class="menuitemClasses"
        >
          <MenuItem name="0-1" @click="SidebarToggle" class="back-icon">
            <Icon type="ios-arrow-back" />
            <span>{{ t("app.back") }}</span>
          </MenuItem>
          <MenuItem name="0-2" @click="changePath('/home')">
            <Icon type="ios-home" />
            <span>{{ t("sidebar.home") }}</span>
          </MenuItem>
          <Submenu
            v-for="(path, index) in sidebars"
            :key="`sidebar-${index}`"
            :name="index"
            @click="changePath(path.path)"
          >
            <template #title>
              <Icon type="ios-calculator" />
              {{ t(path.title) }}
            </template>
            <MenuItem
              v-for="(child, childIndex) in path.list"
              :key="`sidebar-${child.path}`"
              :name="`${index}-${childIndex}`"
              @click="changePath(child.path)"
              >{{ t(child.title) }}</MenuItem
            >
          </Submenu>
        </Menu>
        <!-- <Menu
          active-name="1-2"
          theme="dark"
          width="auto"
          :class="menuitemClasses"
        >
          <Submenu name="1">
            <template #title>
              <Icon type="ios-calculator" />
              Item 1
            </template>
            <MenuItem name="1-1">Option 1</MenuItem>
            <MenuItem name="1-2">Option 2</MenuItem>
            <MenuItem name="1-3">Option 3</MenuItem>
          </Submenu>
        </Menu> -->
      </Sider>
      <Layout class="layout-main">
        <Header :style="{ padding: 0 }" class="layout-header-bar">
          <Icon
            @click="SidebarToggle"
            :class="rotateIcon"
            :style="{ margin: '0 20px' }"
            type="md-menu"
            size="24"
            class="header-icon"
          ></Icon>
        </Header>
        <Content
          :style="{ margin: '20px', background: '#fff', minHeight: '260px' }"
        >
          <slot />
        </Content>
      </Layout>
    </Layout>
  </div>
</template>
<script>
// import { useRouter } from "vue-router";
import { computed, ref } from "vue";
import { useI18n } from "@/hooks/use-i18n";
import { useRouter } from "vue-router";

export default {
  setup() {
    const { t } = useI18n();
    const router = useRouter();
    const isCollapsed = ref(false);
    const showMobileSidebar = ref(false);
    const rotateIcon = computed(() => [
      "menu-icon",
      isCollapsed.value ? "rotate-icon" : "",
    ]);
    const menuitemClasses = computed(() => [
      "menu-item",
      isCollapsed.value ? "collapsed-menu" : "",
    ]);

    const sidebars = {
      count: {
        title: "sidebar.count.title",
        path: null,
        list: [
          {
            title: "sidebar.count.cost",
            path: "/cost",
          },
          {
            title: "sidebar.count.income",
            path: "/income",
          },
        ],
      },
    };

    const changePath = (path) => {
      if (!path) return;
      router.push(path);
    };

    const SidebarToggle = () =>
      (showMobileSidebar.value = !showMobileSidebar.value);

    // onMounted(() => {
    /** 後續自動新增sidebar */
    // const router = useRouter();
    // const allRoutesPaths = router.getRoutes().map((route) => {
    //   route.path
    // });
    // console.log(allRoutesPaths); // 打印所有页面的路径
    // });
    return {
      sidebars,
      rotateIcon,
      isCollapsed,
      menuitemClasses,
      showMobileSidebar,
      t,
      changePath,
      SidebarToggle,
    };
  },
};
</script>
<style lang="scss" scoped>
.layout {
  border: 1px solid #d7dde4;
  background: #f5f7f9;
  position: relative;
  border-radius: 4px;
  overflow: hidden;
}
.layout .ivu-menu {
  z-index: 0;
}
.layout-header-bar {
  background: #fff;
  box-shadow: 0 1px 1px rgba(0, 0, 0, 0.1);
}
.layout-logo-left {
  width: 90%;
  height: 30px;
  background: #5b6270;
  border-radius: 3px;
  margin: 15px auto;
}
.menu-icon {
  transition: all 0.3s;
}
.rotate-icon {
  transform: rotate(-90deg);
}
.menu-item span {
  display: inline-block;
  overflow: hidden;
  width: 69px;
  text-overflow: ellipsis;
  white-space: nowrap;
  vertical-align: bottom;
  transition: width 0.2s ease 0.2s;
}
.menu-item i {
  transform: translateX(0px);
  transition: font-size 0.2s ease, transform 0.2s ease;
  vertical-align: middle;
  font-size: 16px;
}
.collapsed-menu span {
  width: 0px;
  transition: width 0.2s ease;
}
.collapsed-menu i {
  transform: translateX(5px);
  transition: font-size 0.2s ease 0.2s, transform 0.2s ease 0.2s;
  vertical-align: middle;
  font-size: 22px;
}

.layout-header-bar {
  display: none;
}

.back-icon {
  display: none;
}

@media (max-width: 520px) {
  .layout-header-bar {
    display: block;
  }
  .back-icon {
    display: block;
  }
  .layout-sidebar {
    position: fixed;
    width: 100% !important;
    max-width: 100% !important;
    height: 100%;
    z-index: 1045;
    transform: translateX(-100%);
    &-show {
      transform: translateX(0);
    }
  }
}
</style>
