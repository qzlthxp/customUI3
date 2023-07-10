<template>
  <view
    class="customthree-tree-select-content"
    :class="{
      border: border && node[dataChildren] && node[dataChildren].length && node.showChildren
    }"
    :style="{ marginLeft: `${level ? 14 : 0}px` }"
  >
    <view v-if="node.visible" class="custom-tree-select-item">
      <view class="item-content">
        <view class="left" @click.stop="handleNameClick(node)">
          <view class="icon-group">
            <view
              v-if="node[dataChildren] && node[dataChildren].length"
              :class="['right-icon', { active: node.showChildren }]"
            >
              <uni-icons type="right" size="14" color="#333"></uni-icons>
            </view>
            <view v-else class="smallcircle-filled">
              <uni-icons class="smallcircle-filled-icon" type="smallcircle-filled" size="10" color="#333"></uni-icons>
            </view>
          </view>
          <view v-if="loadingArr.includes(node[props.dataValue].toString())" class="loading-icon-box">
            <uni-icons class="loading-icon" type="spinner-cycle" size="14" color="#333"></uni-icons>
          </view>
          <view class="name" :style="node.disabled ? 'color: #999' : ''">
            <text>{{ node[dataLabel] }}</text>
          </view>
        </view>
        <view
          v-if="
            choseParent ||
            (!choseParent && !node[dataChildren]) ||
            (!choseParent && node[dataChildren] && !node[dataChildren].length)
          "
          :class="['check-box', { disabled: node.disabled }]"
          @click.stop="!node.disabled && nodeClick(node)"
        >
          <view v-if="!node.checked && node.partChecked && linkage" class="part-checked"></view>
          <uni-icons
            v-if="node.checked"
            type="checkmarkempty"
            size="18"
            :color="node.disabled ? '#333' : '#007aff'"
          ></uni-icons>
        </view>
      </view>
    </view>
    <view v-if="node.showChildren && node[dataChildren] && node[dataChildren].length">
      <data-select-item
        v-for="item in listData"
        :key="item[dataValue]"
        :node="item"
        :dataLabel="dataLabel"
        :dataValue="dataValue"
        :dataChildren="dataChildren"
        :choseParent="choseParent"
        :lazyLoadChildren="lazyLoadChildren"
        :border="border"
        :linkage="linkage"
        :level="level + 1"
      ></data-select-item>
    </view>
  </view>
</template>
<script lang="ts" setup>
import dataSelectItem from './data-select-item.vue'
import { paging } from './utils'
import { ref, inject, watchEffect } from 'vue'

const { nodeClick, nameClick, loadNode, initData, addNode } = inject('nodeFn')

const props = defineProps({
  node: {
    type: Object,
    default: () => ({})
  },
  choseParent: {
    type: Boolean,
    default: true
  },
  dataLabel: {
    type: String,
    default: 'name'
  },
  dataValue: {
    type: String,
    default: 'value'
  },
  dataChildren: {
    type: String,
    default: 'children'
  },
  border: {
    type: Boolean,
    default: false
  },
  linkage: {
    type: Boolean,
    default: false
  },
  lazyLoadChildren: {
    type: Boolean,
    default: false
  },
  level: {
    type: Number,
    default: 0
  }
})

const listData = ref([])
const clearTimerList = ref([])
const loadingArr = ref([])

watchEffect(() => {
  if (props.node.showChildren && props.node[props.dataChildren] && props.node[props.dataChildren].length) {
    resetClearTimerList()
    renderTree(props.node[props.dataChildren])
  }
})

// 中断懒加载
function resetClearTimerList() {
  const list = [...clearTimerList.value]
  clearTimerList.value = []
  list.forEach((fn) => fn())
}

// 懒加载
function renderTree(arr: any[]) {
  const pagingArr = paging(arr)
  listData.value = pagingArr?.[0] || []
  lazyRenderList(pagingArr, 1)
}

// 懒加载具体逻辑
function lazyRenderList(arr: any[], startIndex: number) {
  for (let i = startIndex; i < arr.length; i++) {
    let timer: any = null
    timer = setTimeout(() => {
      listData.value.push(...arr[i])
    }, i * 500)
    clearTimerList.push(() => clearTimeout(timer))
  }
}

// 点击名称懒加载子元素
function handleNameClick(node: any) {
  if (!node.visible) return

  if (!node[props.dataChildren]?.length && props.lazyLoadChildren) {
    loadingArr.value.push(node[props.dataValue].toString())

    loadNode(node)
      .then((res: any) => {
        addNode(node, initData(res, node.visible))
      })
      .finally(() => {
        loadingArr.value = []
      })
  } else {
    nameClick(node)
  }
}
</script>

<style lang="scss" scoped>
$primary-color: #007aff;
$col-sm: 4px;
$col-base: 8px;
$col-lg: 12px;
$row-sm: 5px;
$row-base: 10px;
$row-lg: 15px;
$radius-sm: 3px;
$radius-base: 6px;
$border-color: #c8c7cc;

.customthree-tree-select-content {
  &.border {
    border-left: 1px solid $border-color;
  }

  ::v-deep .uni-checkbox-input {
    margin: 0 !important;
  }

  .item-content {
    margin: 0 0 $col-lg;
    display: flex;
    justify-content: space-between;
    align-items: center;
    position: relative;

    &::after {
      content: '';
      position: absolute;
      top: 0;
      left: 0;
      bottom: 0;
      width: 3px;
      background-color: #fff;
      transform: translateX(-2px);
      z-index: 1;
    }

    .left {
      flex: 1;
      display: flex;
      align-items: center;

      .right-icon {
        transition: 0.15s ease;

        &.active {
          transform: rotate(90deg);
        }
      }

      .smallcircle-filled {
        width: 14px;
        height: 13.6px;
        display: flex;
        align-items: center;

        .smallcircle-filled-icon {
          transform-origin: center;
          transform: scale(0.55);
        }
      }

      .loading-icon-box {
        margin-right: $row-sm;
        width: 14px;
        height: 100%;
        display: flex;
        justify-content: center;
        align-items: center;

        .loading-icon {
          transform-origin: center;
          animation: rotating infinite 0.2s ease;
        }
      }

      .name {
        flex: 1;
      }
    }
  }

  .check-box {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    width: 23.6px;
    height: 23.6px;
    border: 1px solid $border-color;
    border-radius: $radius-sm;
    display: flex;
    justify-content: center;
    align-items: center;

    &.disabled {
      background-color: rgb(225, 225, 225);
    }

    .part-checked {
      width: 60%;
      height: 2px;
      background-color: $primary-color;
    }
  }
}

@keyframes rotating {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
