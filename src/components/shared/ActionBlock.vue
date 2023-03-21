<template>
  <div>
    <b-dropdown right>
      <template #button-content>
        <span>+</span>
      </template>
      <b-dropdown-item v-if="['if_condition', 'then_statement', 'else_statement'].includes(type)" @click="addAndClick">+ And</b-dropdown-item>
      <b-dropdown-item v-if="['then_statement'].includes(type)" @click="addElseClick">+ Else</b-dropdown-item>
      <b-dropdown-item v-if="['if_condition', 'or_condition'].includes(type)" @click="addOrClick">+ Or</b-dropdown-item>
      <b-dropdown-item @click="copyClick">Copy</b-dropdown-item>
      <b-dropdown-item v-if="(type == 'if_condition' && value.rule.conditions.length > 1) || (type == 'then_statement' && value.rule.then.length > 1) || type == 'or_condition' || type == 'else_statement'" @click="deleteClick">Delete</b-dropdown-item>
    </b-dropdown>
  </div>
</template>
  
<script>
  export default {
    name: 'ActionBlock',
    props: ['value', 'type', 'condition_index', 'or_condition_index', 'statement_index'],
    data: () => ({
      
    }),
    methods: {
        addAndClick() {
            if (this.type == 'if_condition') this.value.rule.conditions.push({main: {field:'all', operator:'contains', operand:''}, ors: []});
            else if (this.type == 'then_statement') this.value.rule.then.push({field:'all', operator:'set to value', operand:null});
            else if (this.type == 'else_statement') this.value.rule.else.push({field:'all', operator:'set to value', operand:null});
        },
        addElseClick() {
            if (this.type == 'then_statement') this.value.rule.else.push({field:'all', operator:'set to value', operand:null});
        },
        addOrClick() {
            this.value.rule.conditions[this.condition_index].ors.push({field:'all', operator:'contains', operand:''});
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
  