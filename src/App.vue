<template>
  <div id="app" class="bg-gray-100 min-h-screen">
    <fwb-navbar>
      <template #logo>
        <fwb-navbar-logo alt="Flowbite logo" image-url="https://flowbite.com/images/logo.svg" link="#">
          TaskView
        </fwb-navbar-logo>
      </template>
      <template v-slot:default="{ isShowMenu }">
        <fwb-navbar-collapse :is-show-menu="isShowMenu">
          <fwb-navbar-link is-active link="#">
            Home
          </fwb-navbar-link>
          <fwb-navbar-link link="#">
            Services
          </fwb-navbar-link>
          <fwb-navbar-link link="#">
            Pricing
          </fwb-navbar-link>
          <fwb-navbar-link link="#">
            Contact
          </fwb-navbar-link>
        </fwb-navbar-collapse>
      </template>
    </fwb-navbar>
    <TodoList />
  </div>
</template>

<script setup>
import TodoList from "./components/TodoList.vue";
import {
  FwbNavbar,
  FwbNavbarCollapse,
  FwbNavbarLink,
  FwbNavbarLogo,
} from "flowbite-vue";
</script>
