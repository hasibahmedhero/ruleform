<template>
  <div id="app">
    <RulesForm />
  </div>
</template>

<script>
import RulesForm from './components/RulesForm.vue'

export default {
  name: 'App',
  components: {
    RulesForm
  }
}
</script>

<style>
  label{font-weight:bold; color:#888}
  .input-group-prepend .input-group-text{border-radius:6px 0 0 6px}
  .w-240px{width:240px;}
</style>