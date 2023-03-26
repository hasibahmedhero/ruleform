<template>
  <div>
    <div v-if="operator == 'set to value'">
        <input v-model="set_to_value" type="text" class="rounded w-240px border p-2" placeholder="value">
    </div>

    <div v-else-if="operator == 'append value'">
        <input v-model="append_value" type="text" class="rounded w-240px border p-2" placeholder="value">
    </div>

    <div v-else-if="operator == 'copy value'" class="d-flex gap-2 align-items-center">
        <label>from</label>
        <select v-model="copy_value" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
    </div>

    <div v-else-if="operator == 'combine value'">
        <div style="display:flex; flex-wrap:wrap; column-gap:10px; row-gap:10px;">
          <input v-model="combine_value.text" type="text" class="rounded w-240px border p-2" placeholder="combine value">
          <div v-for="(item, index) in combine_value.fields" :key="index" class="d-flex">
            <select v-model="combine_value.fields[index]" class="border w-240px rounded p-2">
              <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
            </select>
            <span @click="combine_value.fields.splice(index, 1)" style="margin:8px 0 0 2px; cursor:pointer;">&#10006;</span>
          </div>
          <b-button @click="combine_value.fields.push('')" variant="success">Add field</b-button>
        </div>
    </div>

    <div v-else-if="operator == 'search for value'">
        <select v-model="search_for_value.field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
        <div class="d-flex gap-4 mt-3 w-100">
            <div class="w-100">
                <label>Search for value in (part of) field</label>
                <textarea v-model="search_for_value.search" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-100">
                <label>(Optional) use replacement value</label>
                <textarea v-model="search_for_value.replace" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'replace value'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>search</label>
        <input v-model="replace_value.search" type="text" class="rounded w-240px border p-2" placeholder="search value">
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>replace with</label>
        <input v-model="replace_value.replace" type="text" class="rounded w-240px border p-2" placeholder="replace value">
      </div>
    </div>

    <div v-else-if="operator == 'lookup and replace value'">
        <div class="d-flex gap-4 mt-3 w-100">
            <div class="w-100">
                <label>When field is equal to</label>
                <textarea v-model="lookup_and_replace_value.search" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-100">
                <label>Then change value to</label>
                <textarea v-model="lookup_and_replace_value.replace" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'search and replace value'">
        <div class="d-flex gap-4 mt-3 w-100">
            <div class="w-100">
                <label>Search for value in (part of) field</label>
                <textarea v-model="search_and_replace_value.search" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
            <div class="w-100">
                <label>Use replacement value</label>
                <textarea v-model="search_and_replace_value.replace" class="rounded bg-gray-50 border block w-100 p-2 mt-2" placeholder="Enter one value per line"></textarea>
            </div>
        </div>
    </div>

    <div v-else-if="operator == 'split text'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>from</label>
        <select v-model="split_text.field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>split on</label>
        <input v-model="split_text.split_on" type="text" class="rounded w-240px border p-2" placeholder="e.g. ,">
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>use</label>
        <select v-model="split_text.use" class="border w-240px rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>

      <div class="d-flex gap-2 align-items-center">
        <label>until</label>
        <select v-model="split_text.until" class="border w-240px rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'maximum length'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>of</label>
        <input v-model="maximum_length.of" type="text" class="rounded w-240px border p-2" placeholder="e.g., 60">
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>characters and</label>
        <select v-model="maximum_length.characters" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.maximum_length_characters" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'modify text'" class="mt-3">
      <select v-model="modify_text" class="border w-240px rounded p-2">
        <option v-for="(field, field_i) in select_options.modify_text" :key="field_i">{{field}}</option>
      </select>
    </div>

    <div v-else-if="operator == 'add google tracking'" class="mt-3">
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="min-width:110px; background:#eee">Source</span>
            <input v-model="add_google_tracking.source" type="text" class="border p-2" placeholder="e.g., example">
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="min-width:110px; background:#eee">Medium</span>
            <input v-model="add_google_tracking.medium" type="text" class="border p-2" placeholder="e.g., cpc">
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="min-width:110px; background:#eee">Campaign</span>
            <input v-model="add_google_tracking.campaign" type="text" class="border p-2" placeholder="e.g., google">
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="min-width:110px; background:#eee">Term</span>
            <input v-model="add_google_tracking.term" type="text" class="border p-2" placeholder="e.g., title">
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="min-width:110px; background:#eee">Content</span>
            <input v-model="add_google_tracking.content" type="text" class="border p-2" placeholder="e.g., brand">
        </div>
    </div>

    <div v-else-if="operator == 'set to Google Shopping category'">
        <input v-model="set_to_google_shopping_category" type="text" class="rounded w-240px border p-2" placeholder="e.g., shoes">
    </div>

    <div v-else-if="operator == 'round number'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>calculate</label>
        <select v-model="round_number.calculate" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.round_number.calculate" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>to</label>
        <select v-model="round_number.to" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.round_number.to" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'reformat number'" class="mt-3">
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="background:#eee">Decimal separator</span>
            <select v-model="reformat_number.decimal_separator" class="border p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.decimal_separator" :key="field_i">{{field}}</option>
            </select>
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="background:#eee">Thousands separator</span>
            <select v-model="reformat_number.thousands_separator" class="border p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.thousands_separator" :key="field_i">{{field}}</option>
            </select>
        </div>
        <div class="d-flex mb-2">
            <span class="d-inline-flex align-items-center justify-content-start px-3 border" style="background:#eee">Number of decimals</span>
            <select v-model="reformat_number.number_of_decimals" class="border p-2">
                <option v-for="(field, field_i) in select_options.reformat_number.number_of_decimals" :key="field_i">{{field}}</option>
            </select>
        </div>
    </div>

    <div v-else-if="operator == 'calculate'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>take</label>
        <select v-model="calculate.take_field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>and</label>
        <select v-model="calculate.operator" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.calculate.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="calculate.operator.includes('field')" class="d-flex gap-2 align-items-center">
        <label>with</label>
        <select v-model="calculate.by_field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-else class="d-flex gap-2 align-items-center">
        <label>with</label>
        <input v-model="calculate.by" type="text" class="rounded w-240px border p-2" placeholder="e.g. 1.5">
      </div>
    </div>

    <div v-else-if="operator == 'calculate sum'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>of field</label>
        <select v-model="calculate_sum" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate length'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>of field</label>
        <select v-model="calculate_length" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate list length'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>of field</label>
        <select v-model="calculate_list_length" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'group items'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div v-for="(item, index) in group_items" :key="index" class="d-flex gap-2 align-items-center">
        <label v-if="index == 0">combine fields</label>
        <label v-else>and</label>
        <select v-model="group_items[index]" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-1 align-items-center">
        <b-button @click="group_items.push('aff_code')" variant="success">Add field</b-button>
        <b-button @click="group_items.pop()">delete</b-button>
      </div>
    </div>

    <div v-else-if="operator == 'split items'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3"></div>

    <div v-else-if="operator == 'deduplicate items'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <select v-model="deduplicate_items.operator" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.deduplicate_items.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="deduplicate_items.operator && deduplicate_items.operator != 'exclude duplicates'" class="d-flex gap-2 align-items-center">
        <label>of field</label>
        <select v-model="deduplicate_items.by_field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'calculate item group'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <select v-model="calculate_item_group.operator" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.calculate_item_group.operators" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div v-if="calculate_item_group.operator && calculate_item_group.operator != 'count items'" class="d-flex gap-2 align-items-center">
        <label>of</label>
        <select v-model="calculate_item_group.of_field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>store in</label>
        <select v-model="calculate_item_group.store_in_field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'sort list'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>order</label>
        <select v-model="sort_list" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in select_options.sort_list" :key="field_i">{{field}}</option>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'slice list'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>from</label>
        <select v-model="slice_list.field" class="border w-240px rounded p-2">
            <option v-for="(field, field_i) in project_fields" :key="field_i">{{field}}</option>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>use</label>
        <select v-model="slice_list.use" class="border w-240px rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
      <div class="d-flex gap-2 align-items-center">
        <label>until</label>
        <select v-model="slice_list.until" class="border w-240px rounded p-2">
          <optgroup v-for="(group, group_key) in select_options.split_text" :key="group_key" :label="group_key">
            <option v-for="(option, option_i) in group" :key="option_i">{{option}}</option>
          </optgroup>
        </select>
      </div>
    </div>

    <div v-else-if="operator == 'split text to list'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>split on</label>
        <input v-model="split_text_to_list" type="text" class="rounded w-240px border p-2" placeholder="e.g.,">
      </div>
    </div>

    <div v-else-if="operator == 'join list to text'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3">
      <div class="d-flex gap-2 align-items-center">
        <label>split on</label>
        <input v-model="join_list_to_text" type="text" class="rounded w-240px border p-2" placeholder="e.g.,">
      </div>
    </div>

    <div v-else-if="operator == 'deduplicate list'" class="d-flex w-100 gap-4 align-items-start flex-wrap mt-3"></div>

  </div>
</template>
  
<script>
export default {
    name: 'StatementBlockOperands',
    props: ['initialValue', 'operator', 'project_fields', 'operators'],
    data: () => ({
        set_to_value: '',
        append_value: '',
        copy_value: 'aff_code',
        combine_value: {
          text:'',
          fields: []
        },
        search_for_value: {
            field: 'aff_code',
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
            field: 'aff_code',
            split_on: '',
            use: 'first',
            until: 'last'
        },
        maximum_length: {
            of: '',
            characters: 'trim on words',
        },
        modify_text: 'capitalize only first character',
        add_google_tracking: {
            source: '',
            medium: '',
            campaign: '',
            term: '',
            content: '',
        },
        set_to_google_shopping_category: '',
        round_number: {
            calculate: 'closest',
            to: '1 cent',
        },
        reformat_number: {
            decimal_separator: '.',
            thousands_separator: '.',
            number_of_decimals: '2 decimals',
        },
        calculate: {
            take_field: 'aff_code',
            operator: 'multiply by',
            by: '',
            by_field: 'aff_code',
        },
        calculate_sum: 'aff_code',
        calculate_length: 'aff_code',
        calculate_list_length: 'aff_code',
        group_items: ['aff_code'],
        split_items: null,
        deduplicate_items: {
            operator: 'exclude duplicates',
            by_field: 'aff_code',
        },
        calculate_item_group: {
            operator: 'count items',
            of_field: 'aff_code',
            store_in_field: 'aff_code',
        },
        sort_list: 'descending',
        slice_list: {
            field: 'aff_code',
            use: 'first',
            until: 'last',
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
    mounted() {
      if (this.initialValue) {
        if (this.operator == 'set to value') this.set_to_value = this.initialValue;
        else if (this.operator == 'append value') this.append_value = this.initialValue;
        else if (this.operator == 'copy value') this.copy_value = this.initialValue;
        else if (this.operator == 'combine value') this.combine_value = this.initialValue;
        else if (this.operator == 'search for value') this.search_for_value = this.initialValue;
        else if (this.operator == 'replace value') this.replace_value = this.initialValue;
        else if (this.operator == 'lookup and replace value') this.lookup_and_replace_value = this.initialValue;
        else if (this.operator == 'search and replace value') this.search_and_replace_value = this.initialValue;
        else if (this.operator == 'split text') this.split_text = this.initialValue;
        else if (this.operator == 'maximum length') this.maximum_length = this.initialValue;
        else if (this.operator == 'modify text') this.modify_text = this.initialValue;
        else if (this.operator == 'add google tracking') this.add_google_tracking = this.initialValue;
        else if (this.operator == 'set to google shopping category') this.set_to_google_shopping_category = this.initialValue;
        else if (this.operator == 'round number') this.round_number = this.initialValue;
        else if (this.operator == 'reformat number') this.reformat_number = this.initialValue;
        else if (this.operator == 'calculate') this.calculate = this.initialValue;
        else if (this.operator == 'calculate sum') this.calculate_sum = this.initialValue;
        else if (this.operator == 'calculate length') this.calculate_length = this.initialValue;
        else if (this.operator == 'calculate list length') this.calculate_list_length = this.initialValue;
        else if (this.operator == 'group items') this.group_items = this.initialValue;
        else if (this.operator == 'split items') this.split_items = this.initialValue;
        else if (this.operator == 'deduplicate items') this.deduplicate_items = this.initialValue;
        else if (this.operator == 'calculate item group') this.calculate_item_group = this.initialValue;
        else if (this.operator == 'sort list') this.sort_list = this.initialValue;
        else if (this.operator == 'slice list') this.slice_list = this.initialValue;
        else if (this.operator == 'split text to list') this.split_text_to_list = this.initialValue;
        else if (this.operator == 'join list to text') this.join_list_to_text = this.initialValue;
        else if (this.operator == 'deduplicate list') this.deduplicate_list = this.initialValue;
      }
    }
}
</script>
  
<style scoped>
  
</style>
  