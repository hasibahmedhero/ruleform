<template>
    <div class="flex w-full gap-5 items-start">
      <select v-model="value.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
        <option>all</option>
        <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
      </select>

      <select v-if="value.field != 'all'" v-model="value.operator" class="border border-slate-300 focus:outline-none rounded p-2">
        <optgroup v-for="(group, group_key) in operators" :key="group_key" :label="group_key">
          <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
        </optgroup>
      </select>

      <div v-if="value.field != 'all'">
        <div v-if="operators.Multiple.includes(value.operator)">
          <textarea v-model="value.operand" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2" placeholder="Enter one value per line"></textarea>
        </div>
        <div v-else-if="operators.Advanced.includes(value.operator)">
            <input v-model="value.operand" type="text" class="rounded bg-gray-50 border border-slate-300 focus:outline-none block w-full p-2" placeholder="value">
        </div>
        <div v-else>
          <div v-if="!is_operand_dynamic_field" class="flex">
            <span @click="is_operand_dynamic_field = true" class="inline-flex items-center justify-center px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l cursor-pointer" title="Switch between input value or dynamic fields">&#9776;</span>
            <input v-model="value.operand" type="text" class="rounded-none rounded-r bg-gray-50 border border-slate-300 focus:outline-none block w-full p-2">
          </div>
          <div v-if="is_operand_dynamic_field" class="flex">
            <span @click="is_operand_dynamic_field = false" class="inline-flex items-center justify-center px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l cursor-pointer" title="Switch between input value or dynamic fields">&#9776;</span>
            <select v-model="value.operand" class="rounded-none rounded-r bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2">
              <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
            </select>
          </div>
        </div>
      </div>
    </div>
</template>
  
<script>
  export default {
    name: 'ConditionBlock',
    props: ['value', 'project_fields', 'operators'],
    data: () => ({
      is_operand_dynamic_field: false,
    }),
    methods: {
      
    }
  }
</script>
  
<style scoped>
  
</style>
  