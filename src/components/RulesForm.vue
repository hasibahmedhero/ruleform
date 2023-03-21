<template>
    <div>
      <div v-if="!rules.length" class="mx-auto text-center" style="max-width:500px; margin-top:200px;">
        <h3 class="mb-4" style="color:#555">Create your first rule</h3>
        <p class="my-4" style="color:#888">Rules are the foundation of Channable. Rules can fill fields, modify fields, remove items and much more.</p>
        <b-button @click="add_new_rule.dialog = true" variant="success" class="px-4">New Rule</b-button>
      </div>
  
      <div v-if="rules.length" class="p-4">
        <div class="border">
          <div class="d-flex" style="min-height:600px">
            <div class="w-25" style="border-right:1px solid #dee2e6">
              <b-list-group class="rounded-0">
                <b-list-group-item
                    v-for="(item, index) in rules"
                    :key="index"
                    @click="current = index"
                    class="border-0 border-bottom"
                    style="font-weight:500; cursor:pointer"
                    :style="{background: current == index ? '#eee' : 'transparent', color: item.is_paused ? '#888' : '#42B1E1'}"
                >{{item.name}}</b-list-group-item>
              </b-list-group>
              <div class="d-flex gap-2 p-2">
                <b-button squared @click="add_new_rule.dialog = true" variant="outline-dark">
                  <b-icon icon="plus-lg"></b-icon>
                </b-button>
                <b-button squared v-if="rules.length" @click="copyRule" variant="outline-dark">
                  <svg width="24px" height="24px" viewBox="0 0 24 24" fill="currentColor"><path d="M19.53 8L14 2.47C13.8595 2.32931 13.6688 2.25018 13.47 2.25H11C10.2707 2.25 9.57118 2.53973 9.05546 3.05546C8.53973 3.57118 8.25 4.27065 8.25 5V6.25H7C6.27065 6.25 5.57118 6.53973 5.05546 7.05546C4.53973 7.57118 4.25 8.27065 4.25 9V19C4.25 19.7293 4.53973 20.4288 5.05546 20.9445C5.57118 21.4603 6.27065 21.75 7 21.75H14C14.7293 21.75 15.4288 21.4603 15.9445 20.9445C16.4603 20.4288 16.75 19.7293 16.75 19V17.75H17C17.7293 17.75 18.4288 17.4603 18.9445 16.9445C19.4603 16.4288 19.75 15.7293 19.75 15V8.5C19.7421 8.3116 19.6636 8.13309 19.53 8ZM14.25 4.81L17.19 7.75H14.25V4.81ZM15.25 19C15.25 19.3315 15.1183 19.6495 14.8839 19.8839C14.6495 20.1183 14.3315 20.25 14 20.25H7C6.66848 20.25 6.35054 20.1183 6.11612 19.8839C5.8817 19.6495 5.75 19.3315 5.75 19V9C5.75 8.66848 5.8817 8.35054 6.11612 8.11612C6.35054 7.8817 6.66848 7.75 7 7.75H8.25V15C8.25 15.7293 8.53973 16.4288 9.05546 16.9445C9.57118 17.4603 10.2707 17.75 11 17.75H15.25V19ZM17 16.25H11C10.6685 16.25 10.3505 16.1183 10.1161 15.8839C9.8817 15.6495 9.75 15.3315 9.75 15V5C9.75 4.66848 9.8817 4.35054 10.1161 4.11612C10.3505 3.8817 10.6685 3.75 11 3.75H12.75V8.5C12.7526 8.69811 12.8324 8.88737 12.9725 9.02747C13.1126 9.16756 13.3019 9.24741 13.5 9.25H18.25V15C18.25 15.3315 18.1183 15.6495 17.8839 15.8839C17.6495 16.1183 17.3315 16.25 17 16.25Z"/></svg>
                </b-button>
                <b-dropdown squared v-if="rules.length" variant="outline-dark" class="rule_action_dropdown">
                  <template #button-content>
                    <svg fill="currentColor" width="24px" height="24px" viewBox="0 0 24 24"><path d="M4,13.743l-1,.579a1,1,0,0,0-.366,1.366l1.488,2.578a1,1,0,0,0,1.366.366L6.5,18.05a1.987,1.987,0,0,1,1.986,0l.02.011a1.989,1.989,0,0,1,1,1.724V21a1,1,0,0,0,1,1h3a1,1,0,0,0,1-1V19.782a1.985,1.985,0,0,1,.995-1.721l.021-.012a1.987,1.987,0,0,1,1.986,0l1.008.582a1,1,0,0,0,1.366-.366l1.488-2.578A1,1,0,0,0,21,14.322l-1-.579a1.994,1.994,0,0,1-1-1.733v-.021a1.991,1.991,0,0,1,1-1.732l1-.579a1,1,0,0,0,.366-1.366L19.876,5.734a1,1,0,0,0-1.366-.366L17.5,5.95a1.987,1.987,0,0,1-1.986,0L15.5,5.94a1.989,1.989,0,0,1-1-1.724V3a1,1,0,0,0-1-1h-3a1,1,0,0,0-1,1V4.294A1.856,1.856,0,0,1,8.57,5.9l-.153.088a1.855,1.855,0,0,1-1.853,0L5.49,5.368a1,1,0,0,0-1.366.366L2.636,8.312A1,1,0,0,0,3,9.678l1,.579A1.994,1.994,0,0,1,5,11.99v.021A1.991,1.991,0,0,1,4,13.743ZM12,9a3,3,0,1,1-3,3A3,3,0,0,1,12,9Z"/></svg>
                  </template>
                  <b-dropdown-item v-if="rules[current].is_paused" @click="rules[current].is_paused = false" href="#"><svg fill="currentColor" width="20px" height="20px" viewBox="0 0 1920 1920"><path d="M175 .024V1920l1570.845-959.927z" fill-rule="evenodd"/></svg> Play rule</b-dropdown-item>
                  <b-dropdown-item v-if="!rules[current].is_paused" @click="rules[current].is_paused = true" href="#"><svg width="28px" height="28px" viewBox="0 0 1024 1024" style="margin:-3px -2px 0 -4px;"><path d="M307.2 256h153.6v512H307.2V256zm256 512h153.6V256H563.2v512z" fill="currentColor" fill-rule="evenodd"/></svg> Pause rule</b-dropdown-item>
                  <b-dropdown-item @click="deleteRule" href="#"><svg viewBox="0 0 16 16" width="20px" height="20px" fill="currentColor"><g><path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5zm3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0V6z"></path><path fill-rule="evenodd" d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1v1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4H4.118zM2.5 3V2h11v1h-11z"></path></g></svg> Delete rule</b-dropdown-item>
                </b-dropdown>
                <b-button squared @click="exportRules" variant="outline-dark">Export Rules</b-button>
              </div>
            </div>
  
            <div class="w-75 px-4" style="background:#F5F5F5">
              <div class="d-flex py-3 gap-2 mb-4 rule-name">
                <b-input-group prepend="Name">
                    <b-form-input v-model="rules[current].name"></b-form-input>
                </b-input-group>
                <b-button v-if="rules[current].is_paused" @click="rules[current].is_paused = false" variant="outline-dark" style="min-width:130px;"><svg fill="currentColor" width="16px" height="16px" viewBox="0 0 1920 1920"><path d="M175 .024V1920l1570.845-959.927z" fill-rule="evenodd"/></svg> Play rule</b-button>
                <b-button v-if="!rules[current].is_paused" @click="rules[current].is_paused = true" variant="outline-dark" style="min-width:130px;"><svg width="22px" height="22px" viewBox="0 0 1024 1024"><path d="M307.2 256h153.6v512H307.2V256zm256 512h153.6V256H563.2v512z" fill="currentColor" fill-rule="evenodd"/></svg> Pause rule</b-button>
              </div>
  
              <!--If Section-->
              <div class="and_or_container" style="margin-left:160px; margin-bottom:15px;">
                <span @click="rules[current].rule.conditions_type = 'and'" :class="{active:rules[current].rule.conditions_type == 'and'}">AND</span>
                <span @click="rules[current].rule.conditions_type = 'or'" :class="{active:rules[current].rule.conditions_type == 'or'}">OR</span>
              </div>
              <div class="d-flex align-items-start">
                <div class="condition-title">IF Conditions</div>
                <div class="w-100">
                  <div v-for="(condition, condition_i) in rules[current].rule.conditions" :key="condition_i" class="condition-group">
                      <div v-if="condition_i != 0" class="and_or_container" style="position:absolute; margin-top:-57px; margin-left:12px;"><span class="active">{{rules[current].rule.conditions_type}}</span></div>
                      
                      <div style="padding-right:60px;">
                        <div v-for="(row, row_i) in rules[current].rule.conditions[condition_i].rows" :key="row_i" style="margin:10px 0;">
                          <div v-if="row_i != 0" class="and_or_container" style="position:absolute; margin-top:-26px; margin-left:12px;"><span class="active">{{rules[current].rule.conditions[condition_i].type}}</span></div>

                          <div class="p-4 d-flex w-100 align-items-start justify-content-between gap-2" style="padding-left:70px !important;">
                            <span class="remove-row" @click="rules[current].rule.conditions[condition_i].rows.splice(row_i, 1)">&#9866;</span>

                            <ConditionBlock v-model="rules[current].rule.conditions[condition_i].rows[row_i]" :project_fields="project_fields" :operators="condition_block_operators" />
                          </div>
                        </div>
                      </div>
                      <div class="group-action-area">
                        <ActionBlock v-model="rules[current]" type="if_condition" :condition_index="condition_i" />
                      </div>
                  </div>
                </div>
              </div>
              <span @click="rules[current].rule.conditions.push({type:'and', rows:[{field:'all', operator:'contains', operand:''}]})" class="white-button" style="position:relative; left:160px; top:-37px;"><span>+</span> Add New Group</span>
  
              <!--Then Section-->
              <div class="d-flex align-items-start">
                <div class="condition-title">THEN<br>do actions</div>
                <div class="w-100">
                  <div class="condition-group">
                      <div style="padding-right:60px;">
                        <div v-for="(statement, statement_i) in rules[current].rule.then" :key="statement_i" style="margin:10px 0;">
                          <div v-if="statement_i != 0" class="and_or_container" style="position:absolute; margin-top:-26px; margin-left:12px;"><span class="active">AND</span></div>

                          <div class="p-4 d-flex w-100 align-items-start justify-content-between gap-2" style="padding-left:70px !important;">
                            <span class="remove-row" @click="rules[current].rule.then.splice(statement_i, 1)">&#9866;</span>

                            <StatementBlock v-model="rules[current].rule.then[statement_i]"  :project_fields="project_fields" :operators="statement_block_operators"/>
                          </div>
                        </div>
                      </div>
                      <div class="group-action-area">
                        <ActionBlock v-model="rules[current]" type="then_statement" />
                      </div>
                  </div>
                </div>
              </div>
              <span v-if="!rules[current].rule.else.length" @click="rules[current].rule.else.push({field:'all', operator:'set to value', operand:null})" class="white-button" style="position:relative; top:-37px;"><span>+</span> Add Else Group</span>

              <!--Else Section-->
              <div v-if="rules[current].rule.else.length" class="d-flex align-items-start">
                <div class="condition-title">ELSE<br>do actions</div>
                <div class="w-100">
                  <div class="condition-group">
                      <div style="padding-right:60px;">
                        <div v-for="(statement, statement_i) in rules[current].rule.else" :key="statement_i" style="margin:10px 0;">
                          <div v-if="statement_i != 0" class="and_or_container" style="position:absolute; margin-top:-26px; margin-left:12px;"><span class="active">AND</span></div>

                          <div class="p-4 d-flex w-100 align-items-start justify-content-between gap-2" style="padding-left:70px !important;">
                            <span class="remove-row" @click="rules[current].rule.else.splice(statement_i, 1)">&#9866;</span>

                            <StatementBlock v-model="rules[current].rule.else[statement_i]"  :project_fields="project_fields" :operators="statement_block_operators"/>
                          </div>
                        </div>
                      </div>
                      <div class="group-action-area">
                        <ActionBlock v-model="rules[current]" type="else_statement" />
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
      rules: [],
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
          is_paused: false,
          rule: {
            conditions_type: 'and',
            conditions: [{type:'and', rows:[{field:'all', operator:'contains', operand:''}]}],
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
      },
      exportRules() {
        console.log(this.rules);
      }
    }
  }
  </script>
  