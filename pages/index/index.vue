<template>
  <view class="content">
    {{ selectedStr }}
    <customthree-tree-select
      search
      linkage
      clearable
      clearResetSearch
      border
      mutiple
      lazyLoadChildren
      canSelectAll
      :safe-area="false"
      :showChildren="false"
      :listData="listData"
      :load="loadNode"
      dataLabel="text"
      dataValue="value"
      v-model="selectedStr"
    ></customthree-tree-select>
    <!-- {{ JSON.stringify(selectedArr) }}
    <customthree-tree-select
      search
      linkage
      clearable
      clearResetSearch
      border
      mutiple
      canSelectAll
      lazyLoadChildren
      :showChildren="false"
      :listData="listData"
      :load="loadNode"
      dataLabel="text"
      dataValue="value"
      v-model="selectedArr"
    ></customthree-tree-select> -->
  </view>
</template>

<script>
export default {
  data() {
    return {
      selectedStr: '1-1',
      selectedArr: ['1-1'],
      listData: [
        {
          value: '0',
          text: '异步懒加载测试'
        },
        {
          value: '1',
          text: '城市1',
          children: [
            {
              value: '1-11',
              text: '街道11'
            },
            {
              value: '1-1',
              text: '街道1',
              disabled: true
            }
          ]
        },
        {
          value: '2',
          text: '城市2',
          children: [
            {
              value: '2-1',
              text: '街道2'
            }
          ]
        },
        {
          value: '3',
          text: '城市3',
          children: [
            {
              value: '3-1',
              text: '街道3',
              children: [
                {
                  value: '3-1-1',
                  text: '小区1',
                  disabled: true,
                  children: [
                    {
                      value: '3-1-1-1',
                      text: '小区1-1'
                    }
                  ]
                }
              ]
            },
            {
              value: '3-2',
              text: '街道4',
              visible: true,
              children: [
                {
                  value: '3-2-1',
                  text: '小区2'
                }
              ]
            },
            {
              value: '3-3',
              text: '街道5',
              children: [
                {
                  value: '3-3-1',
                  text: '小区3'
                },
                {
                  value: '3-3-2',
                  text: '小区3-1'
                }
              ]
            }
          ]
        }
      ]
    }
  },
  methods: {
    loadNode(node) {
      return new Promise((resolve) => {
        setTimeout(() => {
          const item = this.listData.find((item) => item.value === node.value)
          if (item) {
            resolve([
              {
                value: '0-1',
                text: '懒加载子元素'
              }
            ])
          }
          resolve([])
        }, 500)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.content {
  padding: $uni-spacing-col-sm $uni-spacing-row-sm;
}

.logo {
  height: 200rpx;
  width: 200rpx;
  margin-top: 200rpx;
  margin-left: auto;
  margin-right: auto;
  margin-bottom: 50rpx;
}

.text-area {
  display: flex;
  justify-content: center;
}

.title {
  font-size: 36rpx;
  color: #8f8f94;
}
</style>
