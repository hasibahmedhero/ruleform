<template>
  <div>
    <span class="white-button" @click="addNewConditionClick"><span>+</span> Add New Condition</span>
    <div v-if="type == 'if_condition'" class="and_or_container" style="margin-left:60px;">
      <span @click="andClick" :class="{active:value.rule.conditions[this.condition_index].type == 'and'}">AND</span>
      <span @click="orClick" :class="{active:value.rule.conditions[this.condition_index].type == 'or'}">OR</span>
    </div>
    <svg v-if="(type == 'if_condition' && value.rule.conditions.length > 1) || type == 'else_statement'" @click="deleteClick" class="delete-group" viewBox="0 0 16 16" width="1em" height="1em" fill="currentColor"><g><path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0V6z"></path><path fill-rule="evenodd" d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1v1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4H4.118zM2.5 3V2h11v1h-11z"></path></g></svg>
  </div>
</template>
  
<script>
  export default {
    name: 'ActionBlock',
    props: ['value', 'type', 'condition_index', 'or_condition_index', 'statement_index'],
    data: () => ({
      
    }),
    methods: {
      addNewConditionClick() {
        if (this.type == 'if_condition') this.value.rule.conditions[this.condition_index].rows.push({field:'all', operator:'contains', operand:''});
        else if (this.type == 'then_statement') this.value.rule.then.push({field:'all', operator:'set to value', operand:null});
        else if (this.type == 'else_statement') this.value.rule.else.push({field:'all', operator:'set to value', operand:null});
      },
      andClick() {
        if (this.type == 'if_condition') this.value.rule.conditions[this.condition_index].type = "and";
      },
      orClick() {
        if (this.type == 'if_condition') this.value.rule.conditions[this.condition_index].type = "or";
      },
      deleteClick() {
        if (this.type == 'if_condition') this.value.rule.conditions.splice(this.condition_index, 1);
        else if (this.type == 'then_statement') this.value.rule.then.splice(this.statement_index, 1);
        else if (this.type == 'else_statement') this.value.rule.else.splice(this.statement_index, 1);
      },
    }
  }
</script>
  
<style scoped>
  
</style>
  