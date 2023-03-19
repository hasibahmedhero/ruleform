<template>
  <div>
    <div v-if="operator == 'set to value'">
        <input v-model="set_to_value" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="value">
    </div>

    <div v-else-if="operator == 'append value'">
        <input v-model="append_value" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="value">
    </div>

    <div v-else-if="operator == 'copy value'" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">from</label>
        <select v-model="copy_value" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
    </div>

    <div v-else-if="operator == 'combine value'">
        <input v-model="combine_value" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="combine value">
    </div>

    <div v-else-if="operator == 'search for value'">
        <select v-model="search_for_value.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
        <div class="flex gap-4 mt-4 w-full">
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">Search for value in (part of) field</label>
                <textarea v-model="search_for_value.search" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">(Optional) use replacement value</label>
                <textarea v-model="search_for_value.replace" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'replace value'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">search</label>
        <input v-model="replace_value.search" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="search value">
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">replace with</label>
        <input v-model="replace_value.replace" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="replace value">
      </div>
    </div>

    <div v-else-if="operator == 'lookup and replace value'">
        <div class="flex gap-4 mt-4 w-full">
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">When field is equal to</label>
                <textarea v-model="lookup_and_replace_value.search" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">Then change value to</label>
                <textarea v-model="lookup_and_replace_value.replace" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'search and replace value'">
        <div class="flex gap-4 mt-4 w-full">
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">Search for value in (part of) field</label>
                <textarea v-model="search_and_replace_value.search" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-full">
                <label class="text-lg text-gray-500 font-medium">Use replacement value</label>
                <textarea v-model="search_and_replace_value.replace" class="rounded bg-gray-50 border border-slate-300  focus:outline-none block w-full p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'split text'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">from</label>
        <select v-model="split_text.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">split on</label>
        <input v-model="split_text.split_on" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g. ,">
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">use</label>
        <select v-model="split_text.use" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>

      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">until</label>
        <select v-model="split_text.until" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'maximum length'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of</label>
        <input v-model="maximum_length.of" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g., 60">
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">characters and</label>
        <select v-model="maximum_length.characters" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.maximum_length_characters" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'modify text'" class="mt-4">
      <select v-model="modify_text" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
        <option v-for="(field, field_i) in select_options.modify_text" :key="field_i">{{field}}</option>
      </select>
    </div>

    <div v-else-if="operator == 'add google tracking'" class="mt-4">
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Source</span>
            <input v-model="add_google_tracking.source" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2" placeholder="e.g., example">
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Medium</span>
            <input v-model="add_google_tracking.medium" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2" placeholder="e.g., cpc">
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Campaign</span>
            <input v-model="add_google_tracking.campaign" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2" placeholder="e.g., google">
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Term</span>
            <input v-model="add_google_tracking.term" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2" placeholder="e.g., title">
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Content</span>
            <input v-model="add_google_tracking.content" type="text" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2" placeholder="e.g., brand">
        </div>
    </div>

    <div v-else-if="operator == 'set to Google Shopping category'">
        <input v-model="set_to_google_shopping_category" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g., shoes">
    </div>

    <div v-else-if="operator == 'round number'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">calculate</label>
        <select v-model="round_number.calculate" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.round_number.calculate" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">to</label>
        <select v-model="round_number.to" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.round_number.to" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'reformat number'" class="mt-4">
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Decimal separator</span>
            <select v-model="reformat_number.decimal_separator" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.decimal_separator" :key="field_i">{{field}}</option>
            </select>
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Thousands separator</span>
            <select v-model="reformat_number.thousands_separator" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.thousands_separator" :key="field_i">{{field}}</option>
            </select>
        </div>
        <div class="flex mb-2">
            <span class="inline-flex items-center justify-start px-3 text-sm text-gray-900 bg-gray-200 border border-r-0 border-slate-300 rounded-l" style="min-width:90px">Number of decimals</span>
            <select v-model="reformat_number.number_of_decimals" class="rounded-none rounded-r border border-slate-300  focus:outline-none p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.number_of_decimals" :key="field_i">{{field}}</option>
            </select>
        </div>
    </div>

    <div v-else-if="operator == 'calculate'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">take</label>
        <select v-model="calculate.take_field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">and</label>
        <select v-model="calculate.operator" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.calculate.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="calculate.operator.includes('field')" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">with</label>
        <select v-model="calculate.by_field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-else class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">with</label>
        <input v-model="calculate.by" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g. 1.5">
      </div>
    </div>

    <div v-else-if="operator == 'calculate sum'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of field</label>
        <select v-model="calculate_sum" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate length'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of field</label>
        <select v-model="calculate_length" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate list length'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of field</label>
        <select v-model="calculate_list_length" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'group items'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div v-for="(item, index) in group_items" :key="index" class="flex gap-2 items-center">
        <label v-if="index == 0" class="text-lg text-gray-500 font-medium">combine fields</label>
        <label v-else class="text-lg text-gray-500 font-medium">and</label>
        <select v-model="group_items[index]" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-1 items-center">
        <button @click="group_items.push('')" class="bg-blue-500 hover:bg-blue-700 text-white py-1.5 px-4 rounded">Add field</button>
        <button @click="group_items.pop()" class="border border-slate-400 focus:outline-none hover:bg-gray-200 py-1.5 px-4 rounded">delete</button>
      </div>
    </div>

    <div v-else-if="operator == 'split items'" class="flex w-full gap-5 items-start flex-wrap mt-4"></div>

    <div v-else-if="operator == 'deduplicate items'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <select v-model="deduplicate_items.operator" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.deduplicate_items.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="deduplicate_items.operator && deduplicate_items.operator != 'exclude duplicates'" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of field</label>
        <select v-model="deduplicate_items.by_field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate item group'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <select v-model="calculate_item_group.operator" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.calculate_item_group.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="calculate_item_group.operator && calculate_item_group.operator != 'count items'" class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">of</label>
        <select v-model="calculate_item_group.of_field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">store in</label>
        <select v-model="calculate_item_group.store_in_field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'sort list'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">order</label>
        <select v-model="sort_list" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in select_options.sort_list" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'slice list'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">from</label>
        <select v-model="slice_list.field" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">use</label>
        <select v-model="slice_list.use" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">until</label>
        <select v-model="slice_list.until" class="border border-slate-300 w-44 focus:outline-none rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'split text to list'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">split on</label>
        <input v-model="split_text_to_list" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g.,">
      </div>
    </div>

    <div v-else-if="operator == 'join list to text'" class="flex w-full gap-5 items-start flex-wrap mt-4">
      <div class="flex gap-2 items-center">
        <label class="text-lg text-gray-500 font-medium">split on</label>
        <input v-model="join_list_to_text" type="text" class="rounded border border-slate-300 focus:outline-none p-2" placeholder="e.g.,">
      </div>
    </div>

    <div v-else-if="operator == 'deduplicate list'" class="flex w-full gap-5 items-start flex-wrap mt-4"></div>

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
        replace_value: {
            search: '',
            replace: ''
        },
        lookup_and_replace_value: {
            search: '',
            replace: ''
        },
        search_and_replace_value: {
            search: '',
            replace: ''
        },
        split_text: {
            field: '',
            split_on: '',
            use: '',
            until: ''
        },
        maximum_length: {
            of: '',
            characters: '',
        },
        modify_text: '',
        add_google_tracking: {
            source: '',
            medium: '',
            campaign: '',
            term: '',
            content: '',
        },
        set_to_google_shopping_category: '',
        round_number: {
            calculate: '',
            to: '',
        },
        reformat_number: {
            decimal_separator: '',
            thousands_separator: '',
            number_of_decimals: '',
        },
        calculate: {
            take_field: '',
            operator: '',
            by: '',
            by_field: '',
        },
        calculate_sum: '',
        calculate_length: '',
        calculate_list_length: '',
        group_items: [''],
        split_items: null,
        deduplicate_items: {
            operator: '',
            by_field: '',
        },
        calculate_item_group: {
            operator: '',
            of_field: '',
            store_in_field: '',
        },
        sort_list: '',
        slice_list: {
            field: '',
            use: '',
            until: '',
        },
        split_text_to_list: '',
        join_list_to_text: '',
        deduplicate_list: null,

        select_options: {
            split_text: {
                "From the beginning": ["first", "second", "third", "fourth", "fifth", "sixth", "seventh", "eighth", "ninth", "tenth", "eleventh", "twelfth", "thirteenth", "fourteenth", "fifteenth", "sixteenth", "seventeenth", "eighteenth", "nineteenth", "twentieth"],
                "From the end": ["last", "second last", "third last", "fourth last", "fifth last"]
            },
            maximum_length_characters: ["trim on words", "trim directly"],
            modify_text: ["capitalize only first character", "capitalize first character per word", "capitalize first character per sentence", "lowercase all words", "uppercase all words", "remove all non-numeric characters", "remove digits", "remove linebreaks", "remove unnecessary whitespaces", "remove HTML from text"],
            round_number:{
                calculate: ["closest", "up", "down"],
                to: ["1 cent", "5 cents", "10 cents", "25 cents", "95 cents", "99 cents", "whole number"]
            },
            reformat_number: {
                decimal_separator: [".", ","],
                thousands_separator: [".", ",", "Nothing"],
                number_of_decimals: ["No rounding", "0 decimals", "1 decimal", "2 decimals", "3 decimals", "4 decimals", "5 decimals", "6 decimals"],
            },
            calculate: {
                operators: ["multiply by", "divide by", "plus", "minus", "multiply by field", "divide by field", "plus field", "minus field"]
            },
            group_items: {
                operators: ["multiply by", "divide by", "plus", "minus", "multiply by field", "divide by field", "plus field", "minus field"]
            },
            deduplicate_items: {
                operators: ["exclude duplicates", "keep lowest", "keep highest"]
            },
            calculate_item_group: {
                operators: ["count items", "calculate sum", "find lowest value", "find highest value"]
            },
            sort_list: ["descending", "ascending"],
        }

    }),
    watch: {
        operator() {
            this.$emit('setOperand', null);
        },
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
        search_for_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        replace_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        lookup_and_replace_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        search_and_replace_value(newValue) {
            this.$emit('setOperand', newValue);
        },
        split_text(newValue) {
            this.$emit('setOperand', newValue);
        },
        maximum_length(newValue) {
            this.$emit('setOperand', newValue);
        },
        modify_text(newValue) {
            this.$emit('setOperand', newValue);
        },
        add_google_tracking(newValue) {
            this.$emit('setOperand', newValue);
        },
        set_to_google_shopping_category(newValue) {
            this.$emit('setOperand', newValue);
        },
        round_number(newValue) {
            this.$emit('setOperand', newValue);
        },
        reformat_number(newValue) {
            this.$emit('setOperand', newValue);
        },
        calculate(newValue) {
            this.$emit('setOperand', newValue);
        },
        calculate_sum(newValue) {
            this.$emit('setOperand', newValue);
        },
        calculate_length(newValue) {
            this.$emit('setOperand', newValue);
        },
        calculate_list_length(newValue) {
            this.$emit('setOperand', newValue);
        },
        split_items(newValue) {
            this.$emit('setOperand', newValue);
        },
        deduplicate_items(newValue) {
            this.$emit('setOperand', newValue);
        },
        calculate_item_group(newValue) {
            this.$emit('setOperand', newValue);
        },
        sort_list(newValue) {
            this.$emit('setOperand', newValue);
        },
        slice_list(newValue) {
            this.$emit('setOperand', newValue);
        },
        split_text_to_list(newValue) {
            this.$emit('setOperand', newValue);
        },
        join_list_to_text(newValue) {
            this.$emit('setOperand', newValue);
        },
        deduplicate_list(newValue) {
            this.$emit('setOperand', newValue);
        },
    },
    methods: {
        
    }
}
</script>
  
<style scoped>
  
</style>
  