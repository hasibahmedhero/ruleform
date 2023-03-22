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
.rule-list > div {
  padding:12px 15px;
}

.rule_action_dropdown button {
  border-radius: 0;
}

.rule-name .input-group-text {
  background: #D9D9D9;
  min-width: 160px;
  justify-content: center;
  border-radius: 6px 0 0 6px
}

.condition-title {
  min-width: 160px;
  max-width: 160px;
  padding: 12px 30px;
  background: #D9D9D9;
  font-size: 18px;
  color: #555;
  text-align: center;
  margin-top: 3px;
}

.condition-group {
  position: relative;
  background: #fff;
  margin-bottom: 70px;
  border: 3px solid #EAE4F4;
  border-radius: 6px;
}

.group-action-area {
  background: #F7F4FC;
  border-top: 1px solid #EAE4F4;
  padding: 20px
}

.and_or_container {
  background: #fff;
  border-radius: 30px;
  padding: 7px;
  border: 1px solid #eee;
  display: inline-block;
  text-transform: uppercase;
}

.and_or_container span {
  padding: 2px;
  cursor: pointer;
  border-radius: 30px;
  font-weight: 500;
  display: inline-block;
  width: 60px;
  text-align: center;
  color: #777;
}

.and_or_container span.active {
  background: #5c53c2;
  color: #fff;
  cursor: initial;
}

.white-button {
  border: 1px solid #ddd;
  font-weight: 500;
  border-radius: 99px;
  padding: 8px 20px;
  background: #fff;
  box-shadow: 0 8px 20px #ddd;
  cursor: pointer;
  color: #777;
  display: inline-block;
}

.white-button:hover {
  box-shadow: 0 8px 20px #bbb;
}

.white-button span {
  background: #555;
  color: #fff;
  line-height: 20px;
  width: 20px;
  display: inline-block;
  text-align: center;
  border-radius: 99px;
  transform: scale(1.2) translateX(-5px);
}

.remove-row {
  font-size: 12px;
  background: #F9D8DC;
  line-height: 20px;
  width: 21px;
  text-align: center;
  color: #EB6576;
  border-radius: 99px;
  cursor: pointer;
  margin-top: 11px;
}

.delete-group {
  position: absolute;
  top: 35px;
  right: 24px;
  background: #fff;
  color: red;
  padding: 8px;
  width: 35px;
  height: 35px;
  border-radius: 99px;
  border: 1px solid #eee;
  box-shadow: 0 8px 20px #ddd;
  cursor: pointer;
}

.block-inputs .w-240px {
  width: 200px;
}

.block-inputs label {
  font-weight: 500;
  color: #888;
  text-transform: capitalize
}

.block-inputs select {
  box-shadow: 0 8px 20px #ddd;
}

.block-inputs input {
  box-shadow: 0 8px 20px #ddd;
}

.block-inputs textarea {
  box-shadow: 0 8px 20px #ddd;
}
</style>