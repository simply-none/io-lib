<template>
  <el-table
    :data="tableData"
    style="width: 100%;margin-bottom: 20px;"
    :row-key="rowKey"
    border
    ref="treeTable"
    :expand-row-keys="expandRow"
    @cell-mouse-enter.once='rowDrop'
    :tree-props="treeProps">
    <slot></slot>
  </el-table>
</template>

<script>
import Sortable from 'sortablejs'
export default {
  props: {
    data: {
      type: Array,
      default: () => []
    },
    treeProps: {
      type: Object,
      default: () => {
        return { children: 'children', hasChildren: 'hasChildren' }
      }
    },
    rowKey: {
      type: String,
      default: 'id'
    },
    expandLevel: {
      type: Number,
      default: 3
    }
  },
  watch: {
    data: {
      handler: function(val) {
        this.tableData = JSON.parse(JSON.stringify(val))
        this.getExpandRow(val)
      },
      deep: true,
      immediate: true
    }
  },
  data() {
    return {
      selectObj: {},
      tableData: [],
      flattArray: [],
      expandRow: []
    }
  },
  mounted() {
    this.getDealData()
  },
  methods: {
    getExpandRow(data) {
      const result = []
      const children = this.treeProps.children
      const rowKey = this.rowKey
      let level = 0
      // 默认展开三级
      const func = (arr) => {
        arr.forEach(item => {
          if (item[children] && item[children].length !== 0) {
            if (level < this.expandLevel) {
              result.push(item[rowKey] + '')
            }
            level++
            func(item[children], item)
          }
        })
      }
      func(data)
      this.expandRow = result
    },
    getDealData() {
      const result = []
      const children = this.treeProps.children
      const rowKey = this.rowKey
      const func = function(arr, parent) {
        arr.forEach(item => {
          const obj = Object.assign(item)
          if (parent) {
            if (obj.parentIds) {
              obj.parentIds.push(parent[rowKey])
            } else {
              obj.parentIds = [parent[rowKey]]
            }
          }
          // 将每一级的数据都一一装入result，不需要删除下面的children，方便拖动的时候根据下标直接拿到整个数据，包括当前节点的子节点
          result.push(obj)
          if (item[children] && item[children].length !== 0) {
            func(item[children], item)
          }
        })
      }
      func(this.tableData)
      this.flattArray = result
    },
    rowDrop() {
      const tbody = document.querySelector('.el-table__body-wrapper tbody')
      const self = this
      Sortable.create(tbody, {
        onEnd({ newIndex, oldIndex }) {
          self.getDealData()
          const sourceObj = self.flattArray[oldIndex]
          const targetObj = self.flattArray[newIndex]
          // 改变要显示的树形数据
          self.changeData(sourceObj, targetObj)
        }
      })
    },
    changeData(sourceObj, targetObj) {
      let flag = 0
      const data = Object.assign(this.tableData)
      const children = this.treeProps.children
      const rowKey = this.rowKey
      const func = function(arr, parent) {
        for (let i = arr.length - 1; i >= 0; i--) {
          const item = arr[i]
          // 判断是否是原来拖动的节点，如果是，则删除
          if (item[rowKey] === sourceObj[rowKey] && (!parent || parent && parent[rowKey] !== targetObj[rowKey])) {
            arr.splice(i, 1)
            flag++
          }
          // 判断是否是需要插入的节点，如果是，则装入数据
          if (item[rowKey] === targetObj[rowKey]) {
            if (item[children]) {
              // 判断源数据是否已经是在目标节点下面的子节点，如果是则不移动了
              let repeat = false
              item[children].forEach(e => {
                if (e[rowKey] === sourceObj[rowKey]) {
                  repeat = true
                }
              })
              if (!repeat) {
                sourceObj.parentIds = []
                item[children].unshift(sourceObj)
              }
            } else {
              sourceObj.parentIds = []
              item[children] = [sourceObj]
            }
            flag++
          }
          // 判断是否需要循环下一级，如果需要则进入下一级
          if (flag !== 2 && item[children] && item[children].length !== 0) {
            func(item[children], item)
          } else if (flag === 2) {
            break
          }
        }
      }
      // 检测是否是将父级拖到子级下面，如果是则数据不变，界面重新回到原数据
      if (targetObj.parentIds) {
        if (targetObj.parentIds.findIndex(_ => _ === sourceObj[this.rowKey]) === -1) {
          func(data)
        } else {
          this.$message.error('不能将父级拖到子级下面')
        }
      } else {
        func(data)
      }
      this.$set(this, 'tableData', [])
      // 重新渲染表格，用doLayout不生效，所以重新装了一遍
      this.$nextTick(() => {
        this.$set(this, 'tableData', data)
      })
    }
  }
}
</script>

<style>

</style>
