<template>
  <div>
    <div v-if="!rules.length" class="mt-44 max-w-md mx-auto text-center">
      <h3 class="text-2xl my-4 text-gray-800">Create your first rule</h3>
      <p class="my-4 text-gray-500">Rules are the foundation of Channable. Rules can fill fields, modify fields, remove items and much more.</p>
      <button @click="add_new_rule.dialog = true" class="bg-blue-500 hover:bg-blue-700 text-white py-1.5 px-4 rounded inline-flex items-center gap-2">
        <svg fill="#ffffff" height="16px" width="16px" viewBox="0 0 492 492"><path d="M465.064,207.566l0.028,0H284.436V27.25c0-14.84-12.016-27.248-26.856-27.248h-23.116 c-14.836,0-26.904,12.408-26.904,27.248v180.316H26.908c-14.832,0-26.908,12-26.908,26.844v23.248 c0,14.832,12.072,26.78,26.908,26.78h180.656v180.968c0,14.832,12.064,26.592,26.904,26.592h23.116 c14.84,0,26.856-11.764,26.856-26.592V284.438h180.624c14.84,0,26.936-11.952,26.936-26.78V234.41 C492,219.566,479.904,207.566,465.064,207.566z"></path></svg>
        New Rule
      </button>
    </div>

    <div v-if="rules.length" class="p-4">
      <div class="border border-slate-200 bg-white">
        <div class="flex justify-between items-center p-4">
          <h3 class="text-xl text-gray-800">Rules</h3>
          <a href="#" class="border border-slate-300 px-2 py-1 text-sky-500">What are rules?</a>
        </div>
        <div class="border-t border-slate-200 flex" style="min-height:600px">
          <div class="w-2/6">
            <ul>
              <li v-for="(item, index) in rules" :key="index"
                class="border-b border-slate-200 px-3 py-3 text-sky-500 flex items-center justify-between cursor-pointer"
                :class="{'bg-gray-100': current == index}"
                @click="current = index"
              >
                {{item.name}}
                <input type="checkbox" class="w-4 h-4 text-blue-600 bg-gray-100 rounded" />
              </li>
            </ul>
            <div class="flex gap-2">
              <button @click="add_new_rule.dialog = true" class="border border-slate-400 focus:outline-none hover:bg-gray-200 rounded py-1.5 px-4 mt-4 ml-3">Add New</button>
              <button v-if="this.rules.length" @click="copyRule" class="border border-slate-400 focus:outline-none hover:bg-gray-200 rounded py-1.5 px-4 mt-4 ml-3">Copy</button>
              <button v-if="this.rules.length" @click="deleteRule" class="border border-slate-400 focus:outline-none hover:bg-gray-200 rounded py-1.5 px-4 mt-4 ml-3">Delete</button>
            </div>
          </div>

          <div class="w-4/6 border-l border-slate-200 px-5" style="background:#fdfdfd">
            <div class="flex py-5 gap-2">
              <div class="flex w-full">
                <span class="inline-flex items-center justify-center px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:80px">Name</span>
                <input v-model="rules[current].name" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none block w-full p-2">
              </div>
              <button class="border border-slate-300 focus:outline-none hover:bg-gray-200 py-1.5 px-4 rounded">Description</button>
            </div>

            <!--If Section-->
            <div>
              <div v-for="(condition, condition_i) in rules[current].rule.conditions" :key="condition_i" class="mb-5">
                <div class="flex border border-slate-300">
                  <div class="inline-flex items-center justify-center border-r border-slate-300 text-center text-3xl text-gray-500 bg-gray-200" style="min-width:80px">{{condition_i == 0 ? 'If' : 'And'}}</div>
                  <div class="p-5 flex w-full items-center gap-2">
                    <ConditionBlock v-model="condition.main" :project_fields="project_fields" :operators="condition_block_operators" />
                    <ActionBlock v-model="rules[current]" type="if_condition" :condition_index="condition_i" />
                  </div>
                </div>
                <div v-if="condition.ors.length" style="padding-left:80px">
                  <div v-for="(or_condition, or_condition_i) in condition.ors" :key="or_condition_i" class="flex border border-slate-300">
                    <div class="inline-flex items-center justify-center border-r border-slate-300 text-center text-xl text-gray-500 bg-gray-200" style="min-width:40px">Or</div>
                    <div class="p-5 flex w-full items-center gap-2">
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
                <div class="flex border border-slate-300">
                  <div class="inline-flex items-center justify-center border-r border-slate-300 text-center text-3xl text-gray-500 bg-gray-200" style="min-width:80px">{{statement_i == 0 ? 'Then' : 'And'}}</div>
                  <div class="p-5 flex w-full items-center gap-2">
                    <StatementBlock v-model="rules[current].rule.then[statement_i]"  :project_fields="project_fields" :operators="statement_block_operators"/>
                    <ActionBlock v-model="rules[current]" type="then_statement" :statement_index="statement_i" />
                  </div>
                </div>
              </div>
            </div>

            <!--Else Section-->
            <div v-if="rules[current].rule.else.length" class="mt-10">
              <div v-for="(statement, statement_i) in rules[current].rule.else" :key="statement_i" class="mb-5">
                <div class="flex border border-slate-300">
                  <div class="inline-flex items-center justify-center border-r border-slate-300 text-center text-3xl text-gray-500 bg-gray-200" style="min-width:80px">{{statement_i == 0 ? 'Else' : 'And'}}</div>
                  <div class="p-5 flex w-full items-center gap-2">
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

    <div v-if="add_new_rule.dialog" class="fixed top-0 bottom-0 left-0 right-0 z-50 flex items-center justify-center" style="background:#000000BB">
      <div class="rounded" style="width:420px; max-width:98%; background:#fff;">
        <h3 class="text-center text-xl text-gray-800 my-4">New Rule</h3>
        <hr />
        <div class="p-5">
          <label class="block text-gray-600 text-base font-medium">Name:</label>
          <input v-model="add_new_rule.data.name" class="border border-slate-400 focus:outline-none rounded p-2 w-full my-2" type="text" placeholder="e.g., Cleanup of products " />
        </div>
        <hr />
        <div class="p-5 flex justify-between">
          <button @click="addNewRuleClick" class="bg-blue-500 hover:bg-blue-700 text-white py-1.5 px-4 rounded">Create</button>
          <button @click="add_new_rule.dialog = false" class="border border-slate-400 focus:outline-none hover:bg-gray-200 py-1.5 px-4 rounded">Close</button>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import ConditionBlock from "./shared/ConditionBlock";
import StatementBlock from "./shared/StatementBlock";
import ActionBlock from "./shared/ActionBlock";
export default {
  components: {ConditionBlock, StatementBlock, ActionBlock},
  name: 'Rules',
  data: () => ({
    rules: [
      {
        name:'test',
        description: '',
        rule: {
          conditions: [{main: {field:'all', operator:'', operand:''}, ors: []}],
          then: [{field:'all', operator:'', operand:null}],
          else:[],
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
          conditions: [{main: {field:'all', operator:'', operand:''}, ors: []}],
          then: [{field:'all', operator:'', operand:null}],
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

<style scoped>

</style>
