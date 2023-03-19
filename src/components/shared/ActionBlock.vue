<template>
  <div class="relative">
    <button 
      @click="is_open = true"
      @blur="is_open = false"
      class="border border-slate-400 focus:outline-none hover:bg-gray-200 px-3 pb-1 rounded flex items-center gap-1">
      <span class="text-3xl font-black">+</span> 
      <span class="text-lg pt-3">&#9662;</span>
    </button>
    <div v-if="is_open" class="absolute right-0 z-40 w-44 border border-slate-100 mt-2 bg-white shadow-2xl">
      <h4 class="text-center py-1 text-gray-500">Options</h4>
      <hr />
      <ul class="py-1">
        <li v-if="['if_condition', 'then_statement', 'else_statement'].includes(type)" @mousedown="addAndClick" class="px-4 py-1.5 cursor-pointer hover:bg-gray-100">+ And</li>
        <li v-if="['then_statement'].includes(type)" @mousedown="addElseClick" class="px-4 py-1.5 cursor-pointer hover:bg-gray-100">+ Else</li>
        <li v-if="['if_condition', 'or_condition'].includes(type)" @mousedown="addOrClick" class="px-4 py-1.5 cursor-pointer hover:bg-gray-100">+ Or</li>
        <li @mousedown="copyClick" class="px-4 py-1.5 cursor-pointer hover:bg-gray-100">Copy</li>
        <li v-if="(type == 'if_condition' && value.rule.conditions.length > 1) || (type == 'then_statement' && value.rule.then.length > 1) || type == 'or_condition' || type == 'else_statement'" @mousedown="deleteClick" class="px-4 py-1.5 cursor-pointer hover:bg-gray-100">Delete</li>
      </ul>
    </div>
  </div>
</template>
  
<script>
  export default {
    name: 'ActionBlock',
    props: ['value', 'type', 'condition_index', 'or_condition_index', 'statement_index'],
    data: () => ({
        is_open: false
    }),
    methods: {
        addAndClick() {
            if (this.type == 'if_condition') this.value.rule.conditions.push({main: {field:'all', operator:'', operand:''}, ors: []});
            else if (this.type == 'then_statement') this.value.rule.then.push({field:'all', operator:'', operand:null});
            else if (this.type == 'else_statement') this.value.rule.else.push({field:'all', operator:'', operand:null});
        },
        addElseClick() {
            if (this.type == 'then_statement') this.value.rule.else.push({field:'all', operator:'', operand:null});
        },
        addOrClick() {
            this.value.rule.conditions[this.condition_index].ors.push({field:'all', operator:'', operand:''});
        },
        copyClick() {
            if (this.type == 'if_condition') {
                const newInstance = JSON.parse(JSON.stringify(this.value.rule.conditions[this.condition_index]));
                this.value.rule.conditions.push(newInstance);

            } else if (this.type == 'or_condition') {
                const newInstance = JSON.parse(JSON.stringify(this.value.rule.conditions[this.condition_index].ors[this.or_condition_index]));
                this.value.rule.conditions[this.condition_index].ors.push(newInstance);

            } else if (this.type == 'then_statement') {
                const newInstance = JSON.parse(JSON.stringify(this.value.rule.then[this.statement_index]));
                this.value.rule.then.push(newInstance);

            } else if (this.type == 'else_statement') {
                const newInstance = JSON.parse(JSON.stringify(this.value.rule.else[this.statement_index]));
                this.value.rule.else.push(newInstance);
            }
        },
        deleteClick() {
            if (this.type == 'if_condition') this.value.rule.conditions.splice(this.condition_index, 1);
            else if (this.type == 'or_condition') this.value.rule.conditions[this.condition_index].ors.splice(this.or_condition_index, 1);
            else if (this.type == 'then_statement') this.value.rule.then.splice(this.statement_index, 1);
            else if (this.type == 'else_statement') this.value.rule.else.splice(this.statement_index, 1);
        },
    }
  }
</script>
  
<style scoped>
  
</style>
  