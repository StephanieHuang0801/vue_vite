<template>
  <el-cascader
    :options="options"
    :props="props"
    clearable
    v-model="casVal"
    @change="handleChange"
    ref="refCas"
  ></el-cascader>
</template>


<script lang="ts">
import {
  defineComponent,
  reactive,
  toRefs,
  computed,
  ref,
  nextTick,
} from "vue";
import { regionlist } from "./area";
export default defineComponent({
  name: "APP",
  setup: function (props, context) {
    const state = reactive({
      props: { multiple: true, checkStrictly: true },
      casVal: [],
    });
    const refCas = ref();
    let options = ref(JSON.parse(JSON.stringify(regionlist)));
    const clear = (arr: any, flag: boolean) => {
      // arr是要禁用的节点集
      arr.forEach((item: any) => {
        item.disabled = flag;
        state.casVal.reverse();
        if (flag == true) {
          // 禁用的同时，如果禁用的item的父节点被选中，要把子节点同步清空
          let currentValue = state.casVal.map((item: any) => {
            return item.slice(-1)[0];
          });
          // 上海 江苏
          if (currentValue.indexOf(item.value) != -1) {
            let index = currentValue.indexOf(item.value);
            state.casVal.splice(index, 1);
          }
        }
        if (item.children) {
          clear(item.children, flag);
        }
      });
    };
    const find = (arr: any, val: any) => {
      for (let k = 0; k < arr.length; k++) {
        let flag = false;
        for (let i = 0; i < val.length; i++) {
          if (val[i][val[i].length - 1] === arr[k].value) {
            flag = true;
          }
        }
        if (flag) {
          arr[k].children && clear(arr[k].children, true);
        }
        // 遍历选择节点，如有一级，禁用一级子节点；如有二级，同理
        if (!flag && arr[k].children) {
          find(arr[k].children, val);
        }
      }
    };
    const handleChange = (val: any) => {
      clear(options.value, false);
      find(options.value, val);
    };
    return {
      ...toRefs(state),
      refCas,
      handleChange,
      options,
    };
  },
});
</script>
