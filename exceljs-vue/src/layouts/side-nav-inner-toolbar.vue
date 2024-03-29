<template>
  <div class="side-nav-inner-toolbar">
    <dx-drawer
      class="drawer"
      position="before"
      template="menu"
      :opened.sync="menuOpened"
      :opened-state-mode="drawerOptions.menuMode"
      :reveal-mode="drawerOptions.menuRevealMode"
      :min-size="drawerOptions.minMenuSize"
      :shading="drawerOptions.shaderEnabled"
      :close-on-outside-click="drawerOptions.closeOnOutsideClick"
    >
      <div class="container">
        <header-toolbar
          :menu-toggle-enabled="headerMenuTogglerEnabled"
          :toggle-menu-func="toggleMenu"
        />
        <dx-scroll-view class="layout-body with-footer">
          <slot />
          <slot name="footer" />
        </dx-scroll-view>
      </div>
      <!-- eslint-disable vue/no-unused-vars -->
      <side-nav-menu
        slot="menu"
        slot-scope="_"
        class="dx-swatch-additional"
        :compact-mode="!menuOpened"
        :items="menuItems"
        @click="handleSideBarClick"
      >
      <!-- eslint-enable-->
        <dx-toolbar id="navigation-header">
          <dx-item location="before" css-class="menu-button">
            <!-- eslint-disable vue/no-unused-vars -->
            <dx-button
              icon="menu"
              styling-mode="text"
              @click="toggleMenu"
              slot-scope="_"
            />
            <!-- eslint-enable -->
          </dx-item>
          <dx-item location="before" css-class="header-title dx-toolbar-label">
            <!-- eslint-disable vue/no-unused-vars -->
            <div slot-scope="_">
              <div>{{ title }}</div>
            </div>
            <!-- eslint-enable -->
          </dx-item>
        </dx-toolbar>
      </side-nav-menu>
    </dx-drawer>
  </div>
</template>

<script>
import DxButton from "devextreme-vue/button";
import DxDrawer from "devextreme-vue/drawer";
import DxScrollView from "devextreme-vue/scroll-view";
import DxToolbar, { DxItem } from "devextreme-vue/toolbar";

import HeaderToolbar from "../components/header-toolbar";
import SideNavMenu from "../components/side-nav-menu";
import menuItems from "../app-navigation";

export default {
  props: {
    title: String,
    isXSmall: Boolean,
    isLarge: Boolean
  },
  methods: {
    toggleMenu(e) {
      const pointerEvent = e.event;
      pointerEvent.stopPropagation();
      if (this.menuOpened) {
        this.menuTemporaryOpened = false;
      }
      this.menuOpened = !this.menuOpened;
    },
    handleSideBarClick() {
      if (this.menuOpened === false) this.menuTemporaryOpened = true;
      this.menuOpened = true;
    }
  },
  data() {
    return {
      menuOpened: this.isLarge,
      menuTemporaryOpened: false,
      menuItems
    };
  },
  computed: {
    drawerOptions() {
      const shaderEnabled = !this.isLarge;
      return {
        menuMode: this.isLarge ? "shrink" : "overlap",
        menuRevealMode: this.isXSmall ? "slide" : "expand",
        minMenuSize: this.isXSmall ? 0 : 60,
        menuOpened: this.isLarge,
        closeOnOutsideClick: shaderEnabled,
        shaderEnabled
      };
    },
    headerMenuTogglerEnabled() {
      return this.isXSmall;
    }
  },
  watch: {
    isLarge() {
      if (!this.menuTemporaryOpened) {
        this.menuOpened = this.isLarge;
      }
    },
    $route() {
      if (this.menuTemporaryOpened || !this.isLarge) {
        this.menuOpened = false;
        this.menuTemporaryOpened = false;
      }
    }
  },
  components: {
    DxButton,
    DxDrawer,
    DxScrollView,
    DxToolbar,
    DxItem,
    HeaderToolbar,
    SideNavMenu
  }
};
</script>

<style lang="scss">
.side-nav-inner-toolbar {
  width: 100%;
}

.container {
  height: 100%;
  flex-direction: column;
  display: flex;
}

.layout-body {
  flex: 1;
  min-height: 0;
}

.content {
  flex-grow: 1;
}

#navigation-header {
  @import "../themes/generated/variables.additional.scss";
  background-color: $base-accent;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.12), 0 1px 2px rgba(0, 0, 0, 0.24);

  .menu-button .dx-icon {
    color: $base-text-color;
  }

  .screen-x-small & {
    padding-left: 20px;
  }

  .dx-theme-generic & {
    padding-top: 10px;
    padding-bottom: 10px;
  }
}
</style>
