<template>
  <JsonTreeView :data="state.json" :maxDepth="3" @Selected="onSelected" />
</template>

<script lang="ts">
import { defineComponent, reactive } from "vue";
import { JsonTreeView } from "json-tree-view-vue3";

export default defineComponent({
  components: { JsonTreeView },
//   data (){
//     return {
//         state: '',
//     }
//   },
  setup() {
    // this.state = {
    //   json: `{"string":"text","number":123,"boolean":true,"array":["A","B","C"],"object":{"prop1":"value1","nestedObject":{"prop2":"value2"}}}`
    // }
    let state = reactive({
      json: `{"string":"text","number":123,"boolean":true,"array":["A","B","C"],"object":{"prop1":"value1","nestedObject":{"prop2":"value2"}}}`
    });
    function onSelected(event: unknown) {
        let tem = JSON.parse(state.json);
        tem['string'] = {"apple": "hi"}
        state.json = JSON.stringify(tem);
      console.log(tem);
      console.log(event);
    }
    return {
      state,
      onSelected,
    };
    
  },
  methods: {
    
  }
});
</script>