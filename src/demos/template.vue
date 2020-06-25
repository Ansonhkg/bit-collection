<template>
  <div class="frame">
    <!-- header -->
    <h2>{{ settings.title }}</h2>
    <p>
      <a :href="settings.github" target="_blank">{{ settings.github }}</a>
    </p>
    <hr />
    <h3>Dependencies</h3>
    <ul>
      <li v-for="dependency in settings.dependencies" :key="dependency">{{ dependency}}</li>
    </ul>
    <hr />

    <!-- content -->
    <h3>Preview</h3>
    <slot></slot>
  </div>
</template>

<script>
export default {
  props: ["settings"]
};
</script>