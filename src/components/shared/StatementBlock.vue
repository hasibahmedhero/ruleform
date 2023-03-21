<template>
    <div class="d-flex w-100 gap-4 align-items-start flex-wrap block-inputs">
      <div class="d-flex gap-2 align-items-center">
        <label>take</label>
        <select v-model="value.field" class="border w-240px rounded p-2">
            <option>all</option>
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>

      <div v-if="value.field != 'all'" class="d-flex gap-2 align-items-center">
        <label>and</label>
        <select v-model="value.operator" class="border w-240px rounded p-2">
            <optgroup v-for="(group, group_key) in operators" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
            </optgroup>
        </select>
      </div>

      <div v-if="value.field != 'all'" class="w-100">
        <StatementBlockOperands @setOperand="onSetOperand" :operator="value.operator" :project_fields="project_fields" :operators="operators" />
      </div>
    </div>
</template>
  
<script>
  import StatementBlockOperands from "./StatementBlockOperands";
  export default {
    components: {StatementBlockOperands},
    name: 'StatementBlock',
    props: ['value', 'project_fields', 'operators'],
    data: () => ({
      is_operand_dynamic_field: false,
    }),
    methods: {
      onSetOperand(new_value) {
        this.value.operand = new_value;
      }
    }
  }
</script>
  
<style scoped>
  
</style>
  