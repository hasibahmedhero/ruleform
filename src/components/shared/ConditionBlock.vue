<template>
    <div class="d-flex w-100 gap-4 align-items-start flex-wrap block-inputs">
      <select v-model="value.field" class="border w-240px focus:outline-none rounded p-2">
        <option>all</option>
        <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
      </select>

      <select v-if="value.field != 'all'" v-model="value.operator" class="border w-240px rounded p-2">
        <optgroup v-for="(group, group_key) in operators" :key="group_key" :label="group_key">
          <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
        </optgroup>
      </select>

      <div v-if="value.field != 'all'">
        <div v-if="operators.Multiple.includes(value.operator)">
          <textarea v-model="value.operand" class="rounded border block w-100 p-2" placeholder="Enter one value per line"></textarea>
        </div>
        <div v-else-if="operators.Advanced.includes(value.operator)">
            <input v-model="value.operand" type="text" class="rounded w-240px border block p-2" placeholder="value">
        </div>
        <div v-else>
          <div v-if="!is_operand_dynamic_field" class="d-flex" style="box-shadow: 0 8px 20px #ddd;">
            <span @click="is_operand_dynamic_field = true" class="d-inline-flex align-items-center justify-content-center px-3 border" title="Switch between input value or dynamic fields" style="cursor:pointer; border-radius: 6px 0 0 6px;">&#9707;</span>
            <input v-model="value.operand" type="text" class="border w-240px p-2 shadow-none" style="border-radius: 0 6px 6px 0;">
          </div>
          <div v-if="is_operand_dynamic_field" class="d-flex" style="box-shadow: 0 8px 20px #ddd;">
            <span @click="is_operand_dynamic_field = false" class="d-inline-flex align-items-center justify-content-center px-3 border" title="Switch between input value or dynamic fields" style="cursor:pointer; border-radius: 6px 0 0 6px;">&#9707;</span>
            <select v-model="value.operand" class="border w-240px p-2 shadow-none" style="border-radius: 0 6px 6px 0;">
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
  