<template>
    <div>
      <div v-if="!rules.length" class="mx-auto text-center" style="max-width:500px; margin-top:200px;">
        <h3 class="mb-4" style="color:#555">Create your first rule</h3>
        <p class="my-4" style="color:#888">Rules are the foundation of Channable. Rules can fill fields, modify fields, remove items and much more.</p>
        <b-button @click="add_new_rule.dialog = true" variant="success" class="px-4">New Rule</b-button>
      </div>
  
      <div v-if="rules.length" class="p-4">
        <div class="border">
          <div class="d-flex justify-content-between align-items-center p-3">
            <h3 class="m-0">Rules</h3>
            <a href="#" class="border px-2 py-1">What are rules?</a>
          </div>
          <div class="border-top d-flex" style="min-height:600px">
            <div class="w-25" style="border-right:1px solid #dee2e6">
              <b-list-group class="rounded-0 border-bottom">
                <b-list-group-item
                    v-for="(item, index) in rules"
                    :key="index"
                    :style="{background: current == index ? '#eee' : 'transparent'}"
                    @click="current = index"
                    class="border-0"
                    style="cursor:pointer"
                >{{item.name}}</b-list-group-item>
              </b-list-group>
              <div class="d-flex gap-2 p-2">
                <b-button @click="add_new_rule.dialog = true">Add New</b-button>
                <b-button v-if="this.rules.length" @click="copyRule">Copy</b-button>
                <b-button v-if="this.rules.length" @click="deleteRule">Delete</b-button>
              </div>
            </div>
  
            <div class="w-75 px-4" style="background:#fdfdfd">
              <div class="d-flex py-3 gap-2">
                <b-input-group prepend="Name">
                    <b-form-input v-model="rules[current].name"></b-form-input>
                </b-input-group>
                <b-button v-b-toggle.my-description class="py-1.5 px-4">Description</b-button>
              </div>

              <b-collapse id="my-description">
                <div class="d-flex mb-4">
                    <div class="d-inline-flex align-items-center justify-content-center text-center" style="min-width:120px; border-right:1px solid #dee2e6; background:#eee; font-size:18px;">Description</div>
                    <textarea v-model="rules[current].description" class="border w-100"></textarea>
                </div>
              </b-collapse>
  
              <!--If Section-->
              <div>
                <div v-for="(condition, condition_i) in rules[current].rule.conditions" :key="condition_i" class="mb-4">
                  <div class="d-flex border">
                    <div class="d-inline-flex align-items-center justify-content-center text-center" style="min-width:80px; border-right:1px solid #dee2e6; background:#eee; font-size:30px;">{{condition_i == 0 ? 'If' : 'And'}}</div>
                    <div class="p-4 d-flex w-100 align-items-center justify-content-between gap-2">
                      <ConditionBlock v-model="condition.main" :project_fields="project_fields" :operators="condition_block_operators" />
                      <ActionBlock v-model="rules[current]" type="if_condition" :condition_index="condition_i" />
                    </div>
                  </div>
                  <div v-if="condition.ors.length" style="padding-left:80px">
                    <div v-for="(or_condition, or_condition_i) in condition.ors" :key="or_condition_i" class="d-flex border">
                      <div class="d-inline-flex align-items-center justify-content-center text-center" style="min-width:40px; border-right:1px solid #dee2e6; background:#eee; font-size:20px;">Or</div>
                      <div class="p-4 d-flex w-100 align-items-center justify-content-between gap-2">
                        <ConditionBlock v-model="rules[current].rule.conditions[condition_i].ors[or_condition_i]" :project_fields="project_fields" :operators="condition_block_operators" />
                        <ActionBlock v-model="rules[current]" type="or_condition" :condition_index="condition_i" :or_condition_index="or_condition_i" />
                      </div>
                    </div>
                  </div>
                </div>
              </div>
  
              <!--Then Section-->
              <div class="mt-10">
                <div v-for="(statement, statement_i) in rules[current].rule.then" :key="statement_i" class="mb-5">
                  <div class="d-flex border">
                    <div class="d-inline-flex align-items-center justify-content-center text-center" style="min-width:80px; border-right:1px solid #dee2e6; background:#eee; font-size:30px;">{{statement_i == 0 ? 'Then' : 'And'}}</div>
                    <div class="p-4 d-flex w-100 align-items-center justify-content-between gap-2">
                      <StatementBlock v-model="rules[current].rule.then[statement_i]"  :project_fields="project_fields" :operators="statement_block_operators"/>
                      <ActionBlock v-model="rules[current]" type="then_statement" :statement_index="statement_i" />
                    </div>
                  </div>
                </div>
              </div>
  
              <!--Else Section-->
              <div v-if="rules[current].rule.else.length" class="mt-10">
                <div v-for="(statement, statement_i) in rules[current].rule.else" :key="statement_i" class="mb-5">
                  <div class="d-flex border">
                    <div class="d-inline-flex align-items-center justify-content-center text-center" style="min-width:80px; border-right:1px solid #dee2e6; background:#eee; font-size:30px;">{{statement_i == 0 ? 'Else' : 'And'}}</div>
                    <div class="p-4 d-flex w-100 align-items-center justify-content-between gap-2">
                      <StatementBlock v-model="rules[current].rule.else[statement_i]"  :project_fields="project_fields" :operators="statement_block_operators"/>
                      <ActionBlock v-model="rules[current]" type="else_statement" :statement_index="statement_i" />
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <b-modal v-model="add_new_rule.dialog" title="New Rule">
        <label class="mb-2">Name:</label>
        <b-form-input v-model="add_new_rule.data.name" placeholder="e.g., Cleanup of products"></b-form-input>
        <template #modal-footer>
            <b-button @click="addNewRuleClick" variant="success" class="px-4">Create</b-button>
            <b-button @click="add_new_rule.dialog = false" class="px-4">Close</b-button>
        </template>
      </b-modal>
  
    </div>
  </template>
  
  <script>
  import ConditionBlock from "./shared/ConditionBlock";
  import StatementBlock from "./shared/StatementBlock";
  import ActionBlock from "./shared/ActionBlock";
  export default {
    components: {ConditionBlock, StatementBlock, ActionBlock},
    name: 'RulesForm',
    data: () => ({
      rules: [
        {
          name:'test',
          description: '',
          rule: {
            conditions: [{main: {field:'all', operator:'contains', operand:''}, ors: []}],
            then: [{field:'all', operator:'set to value', operand:null}],
            else: [],
          }
        },
      ],
      current: 0,
      add_new_rule: {
        dialog: false,
        data: {name: ''},
      },
      project_fields: ['aff_code', 'brand', 'id', 'image_urls', 'link', 'price', 'title'],
      condition_block_operators: {
        Text: ["contains", "doesn't contain", "is equal to", "is not equal to", "starts with", "doesn't start with", "ends with", "doesn't end with", "is empty", "isn't empty", "length exceeds", "length doesn't exceed", "word count exceeds", "word count doesn't exceed"],
        Multiple: ["contains any of", "doesn't contain any of", "is equal to any", "isn't equal to any"],
        Number: ["is greater than", "is greater or equal to", "is less than", "is less or equal to", "is in highest", "is in lowest"],
        Advanced: ["matches regex", "doesn't match regex"]
      },
      statement_block_operators: {
        "Text": ["set to value", "append value", "copy value", "combine value", "search for value", "replace value", "lookup and replace value", "search and replace value", "split text", "maximum length", "modify text"],
        "Tracking and categories": ["add google tracking", "set to Google Shopping category"],
        "Math": ["round number", "reformat number", "calculate", "calculate sum", "calculate length", "calculate list length"],
        "Item": ["group items", "split items", "deduplicate items", "calculate item group"],
        "List": ["sort list", "slice list", "split text to list", "join list to text", "deduplicate list"]
      },
    }),
    methods: {
      addNewRuleClick() {
        if (!this.add_new_rule.data.name) {
          alert("Rule name cannot be blank!!")
          return;
        }
        this.rules.push({
          name:this.add_new_rule.data.name,
          description: '',
          rule: {
            conditions: [{main: {field:'all', operator:'contains', operand:''}, ors: []}],
            then: [{field:'all', operator:'set to value', operand:null}],
            else:[],
          }
        });
        this.add_new_rule.dialog = false;
        this.add_new_rule.data.name = '';
      },
      copyRule() {
        const newInstance = JSON.parse(JSON.stringify(this.rules[this.current]));
        this.rules.push(newInstance);
      },
      deleteRule() {
        const currentIndex = this.current;
        if (currentIndex != 0) this.current = currentIndex-1;
        this.rules.splice(currentIndex, 1);
      }
    }
  }
  </script>
  