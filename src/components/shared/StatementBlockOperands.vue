<template>
  <div class="inline-block">
    <div v-if="operator == 'set to value'">
        <input v-model="set_to_value" type="text" class="rounded border border-slate-300 focus:outline-none block w-full p-2" placeholder="value">
    </div>
    <div v-else-if="operator == 'append value'">
        <input v-model="append_value" type="text" class="rounded border border-slate-300 focus:outline-none block w-full p-2" placeholder="value">
    </div>
    <div v-else-if="operator == 'copy value'" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">from</label>
        <select v-model="copy_value" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
    </div>
    <div v-else-if="operator == 'combine value'">
        <input v-model="combine_value" type="text" class="rounded border border-slate-300 focus:outline-none block w-full p-2" placeholder="combine value">
    </div>
    <div v-else-if="operator == 'search for value'">
        <select v-model="search_for_value.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
    </div>





  </div>
</template>
  
<script>
export default {
    name: 'StatementBlockOperands',
    props: ['operator', 'project_fields', 'operators'],
    data: () => ({
        set_to_value: '',
        append_value: '',
        copy_value: '',
        combine_value: '',
        search_for_value: {
            field: '',
            search: '',
            replace: ''
        },
    }),
    watch: {
        set_to_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        append_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        copy_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        combine_value(newValue) {
            this.$emit('setOperand', newValue);
        },
    },
    methods: {
        
    }
}
</script>
  
<style scoped>
  
</style>
  