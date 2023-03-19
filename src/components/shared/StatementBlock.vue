<template>
    <div class="flex w-full gap-5 items-start flex-wrap">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">take</label>
        <select v-model="value.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option>all</option>
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>

      <div v-if="value.field != 'all'" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">and</label>
        <select v-model="value.operator" class="border border-slate-300 focus:outline-none rounded p-2">
            <optgroup v-for="(group, group_key) in operators" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
            </optgroup>
        </select>
      </div>

      <div v-if="value.field != 'all'" class="w-full">
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
  