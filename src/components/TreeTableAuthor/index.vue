<template>
  <div>
    <el-table :data="formatData" :row-style="showRow" v-bind="$attrs">
      <el-table-column
        v-for="(column, index) in columns"
        :key="column.value"
        :label="column.text"
        :width="column.width"
        :render-header="
          index == 0
            ? renderHeader
            : function (h, { column }) {
                return h('div', { style: 'display:inline;line-height:0px' }, [
                  h('span', column.label),
                ]);
              }
        "
      >
        <template slot-scope="scope">
          <!-- 菜单列表前空格 -->
          <span v-if="index === 0">
            <span
              v-for="space in scope.row._level"
              :key="space"
              class="ms-tree-space"
            />
          </span>
          <!-- 菜单列表前空格 -->

          <!-- 菜单列表展开图标 -->
          <span
            class="tree-ctrl"
            v-if="iconShow(index, scope.row)"
            @click="toggleExpanded(scope.$index)"
          >
            <i v-if="!scope.row._expanded" class="el-icon-plus" />
            <i v-else class="el-icon-minus" />
          </span>
          <!-- 菜单列表展开图标 -->

          <!-- 右侧按钮 -->
          <el-checkbox-group
            v-if="Array.isArray(scope.row[column.value])"
            v-model="scope.row.selectchecked"
            @change="
              handleCheckedCitiesChange(scope.$index, scope.row, column.option)
            "
          >
            <el-checkbox
              v-for="interset in scope.row[column.value]"
              :key="interset.id"
              :label="interset.id"
            >
              {{ interset.description }}
            </el-checkbox>
          </el-checkbox-group>
          <!-- 右侧按钮 -->

          <!-- 左侧列表 -->
          <el-checkbox
            v-else
            v-model="scope.row.checkAll"
            :indeterminate="scope.row.isIndeterminate"
            @change="
              handleCheckAllChange(scope.$index, scope.row, column.option)
            "
          >
            {{ scope.row[column.value] }}
          </el-checkbox>
          <!-- 左侧列表 -->
        </template>
      </el-table-column>
      <slot />
    </el-table>
  </div>
</template>

<script>
/**
  Auth: Lei.j1ang
  Created: 2018/1/19-13:59
*/
import treeToArray from './eval'
export default {
  name: 'TreeTable',
  props: {
    data: {
      type: [Array, Object],
      required: true
    },
    columns: {
      type: Array,
      default: () => []
    },
    evalFunc: Function, // eslint-disable-line
    evalArgs: Array, // eslint-disable-line
    expandAll: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
    }
  },
  computed: {
    // 格式化数据源
    formatData: function () {
      let tmp
      if (!Array.isArray(this.data)) {
        tmp = [this.data]
      } else {
        tmp = this.data
      }
      const func = this.evalFunc || treeToArray
      const args = this.evalArgs ? Array.concat([tmp, this.expandAll], this.evalArgs) : [tmp, this.expandAll]
      return func.apply(null, args)
    }
  },
  created() {
    this.defaultSelcet()
  },
  methods: {
    showRow: function (row) {
      const show = (row.row.parent ? (row.row.parent._expanded && row.row.parent._show) : true)
      row.row._show = show
      return show ? 'animation:treeTableShow 1s;-webkit-animation:treeTableShow 1s;' : 'display:none;'
    },
    // 切换下级是否展开
    toggleExpanded: function (trIndex) {
      const record = this.formatData[trIndex]
      record._expanded = !record._expanded
    },
    // 图标显示
    iconShow(index, record) {
      return (index === 0 && record.children && record.children.length > 0)
    },
    // 左侧菜单
    handleCheckAllChange(index, row, opt) {
      this.leftMenu(row, opt).then(res => {
        this.operationStatus(this.data, opt)
      })
    },
    // 左侧按钮全选半选
    leftMenu(row, opt) {
      return new Promise((resolve, reject) => {
        if (row[opt]) {
          const arr = []
          if (row.checkAll) {
            row[opt].forEach(element => {
              arr.push(element.id)
            })
            row.selectchecked = arr
            row.checkAll = true
            row.isIndeterminate = false
          } else {
            row.selectchecked = []
            row.checkAll = false
            row.isIndeterminate = false
          }
        }
        if (row.children) {
          row.children.forEach(val => {
            const arr = []
            if (row.checkAll) {
              val[opt].forEach(element => {
                arr.push(element.id)
              })
              val.selectchecked = arr
              val.checkAll = true
              val.isIndeterminate = false
            } else {
              val.selectchecked = []
              val.checkAll = false
              val.isIndeterminate = false
            }
            if (val.children) {
              this.leftMenu(val, opt)
            }
          })
        }
        resolve()
      })
    },
    // 右侧按钮
    handleCheckedCitiesChange(index, row, opt) {
      row.checkAll = row.selectchecked.length === row[opt].length
      row.isIndeterminate = row.selectchecked.length > 0 && row.selectchecked.length < row[opt].length
      this.operationStatus(this.data, opt)
    },
    // 默认选中操作数据
    defaultSelcet() {
      this.ooo(this.data).then(res => {
        this.operationStatus(this.data, this.columns[0].option)
      })
    },
    ooo(val) {
      return new Promise((resolve, reject) => {
        if (val) {
          val.forEach(el => {
            if (el.selectchecked.length && el.selectchecked.length !== el[this.columns[0].option].length) {
              el.isIndeterminate = true
              el.checkAll = false
            } else if (el.selectchecked.length && el.selectchecked.length === el[this.columns[0].option].length) {
              el.isIndeterminate = false
              el.checkAll = true
            } else {
              el.isIndeterminate = false
              el.checkAll = false
            }
            if (el.children) {
              this.ooo(el.children)
            }
          })
        }
        resolve()
      })
    },
    // 处理全选半选状态
    operationStatus(arrayData, opt) {
      arrayData.forEach(val => {
        const checkAllArr = []
        const isIndeterminateArr = []
        if (val.children) {
          val.children.forEach(el => {
            checkAllArr.push(el.checkAll)
            isIndeterminateArr.push(el.isIndeterminate)
          })
        }
        if (new Set(checkAllArr).size === 1) {
          if (checkAllArr[0] && isIndeterminateArr[0] === false) { // val下一层全选
            if (val.checkAll) {
              this.handleParentData(val, opt, 2)
            } else {
              this.handleParentData(val, opt, 1)
            }
          } else if (checkAllArr[0] && new Set(isIndeterminateArr).size !== 1) { // val下一层

          } else if (!checkAllArr[0] && new Set(isIndeterminateArr).size !== 1) { // val下一层未全选，有半选无半选同时存在

          } else if (!checkAllArr[0] && new Set(isIndeterminateArr).size === 1) { // val下一层为全选，全半选，或全不选
            if (isIndeterminateArr[0] === true) {
              val.checkAll = false
              val.isIndeterminate = true
              if (val.parent) {
                this.handleParentData(val.parent, opt, 1)
              }
            } else {
              if (val.selectchecked !== 0) {
                val.checkAll = false
                val.isIndeterminate = true
                if (val.parent) {
                  this.handleParentData(val.parent, opt, 1)
                }
              } else {
                if (val.parent) {
                  this.handleParentData(val.parent, opt, 3)
                }
              }
            }
          }
        } else if (new Set(checkAllArr).size > 1) {
          val.checkAll = false
          val.isIndeterminate = true
          if (val.parent) {
            this.handleParentData(val.parent, opt, 1)
          }
        } else if (new Set(checkAllArr).size < 1) {
          if (val.checkAll) {
            if (val.parent && val.parent.selectchecked.length === val.parent[opt].length) {
              this.handleParentData(val.parent, opt, 2)
            }
          } else if (val.isIndeterminate) {
            if (val.parent) {
              this.handleParentData(val.parent, opt, 1)
            }
          } else {
            val.checkAll = false
            val.isIndeterminate = false
            if (val.parent) {
              this.handleParentData(val.parent, opt, 3)
            }
          }
        }
        val.children && this.operationStatus(val.children, opt)
      })
    },

    // 处理父级数据
    handleParentData(val, opt, value) {
      const checkAllArr = []
      const isIndeterminateArr = []
      if (val.children) {
        val.children.forEach(el => {
          checkAllArr.push(el.checkAll)
          isIndeterminateArr.push(el.isIndeterminate)
        })
      }
      if (new Set(checkAllArr).size === 1) {
        if (checkAllArr[0] && isIndeterminateArr[0] === false) {
          if (val.selectchecked.length === val[opt].length) {
            val.checkAll = true
            val.isIndeterminate = false
          } else {
            val.checkAll = false
            val.isIndeterminate = true
          }
        } else if (!checkAllArr[0] && new Set(isIndeterminateArr).size !== 1) {
          val.checkAll = false
          val.isIndeterminate = true
        } else if (!checkAllArr[0] && new Set(isIndeterminateArr).size === 1) {
          if (isIndeterminateArr[0] === true) {
            val.checkAll = false
            val.isIndeterminate = true
          } else {
            if (val.selectchecked.length !== 0) {
              val.checkAll = false
              val.isIndeterminate = true
            } else {
              val.checkAll = false
              val.isIndeterminate = false
            }
          }
        }
      } else if (new Set(checkAllArr).size > 1) {
        val.checkAll = false
        val.isIndeterminate = true
      }
      if (val.parent) {
        this.handleParentData(val.parent, opt, value)
      }
    },
    // 表头复选框渲染
    renderHeader(h, { column }) {
      return h(
        'div',
        {
          style: 'display:inline;line-height:0px'
        },
        [
          h('span', column.label),
          h('el-checkbox', {
            style: 'margin-left:5px',
            on: {
              change: ($event, column) => this.select($event, column) // 选中事件 $event, column，这里$event=true,column是input的dom当在select里打印的时候
            }
          })
        ]
      )
    },
    // 表头全选
    select(obj, $event = this.targetEv) {
      const opt = this.columns[0].option
      if (obj === true) {
        this.handleSelect(this.data, opt, 1)
      } else {
        this.handleSelect(this.data, opt, 2)
      }
    },
    handleSelect(arrayData, opt, value) {
      arrayData.forEach(row => {
        if (row[opt]) {
          const arr = []
          row[opt].forEach(element => {
            arr.push(element.id)
          })
          if (value === 1) {
            row.selectchecked = arr
            row.checkAll = true
            row.isIndeterminate = false
            if (row.children) this.handleSelect(row.children, opt, 1)
          } else {
            row.selectchecked = []
            row.checkAll = false
            row.isIndeterminate = false
            if (row.children) this.handleSelect(row.children, opt, 2)
          }
        }
      })
    },
    getAuth() {
      this.$emit('getAuth', this.data)
    }
  }
}
</script>
<style rel="stylesheet/css">
@keyframes treeTableShow {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
@-webkit-keyframes treeTableShow {
  from {
    opacity: 0;
  }
  to {
    opacity: 1;
  }
}
.el-table__body {
  text-align: left;
}
</style>

<style lang="scss" rel="stylesheet/scss" scoped>
footer {
  display: flex;
  justify-content: flex-end;
  margin-top: 15px;
}

$color-blue: #2196f3;
$space-width: 18px;
.ms-tree-space {
  position: relative;
  top: 1px;
  display: inline-block;
  font-style: normal;
  font-weight: 400;
  line-height: 1;
  width: $space-width;
  height: 14px;
  &::before {
    content: "";
  }
}
.processContainer {
  width: 100%;
  height: 100%;
}
table td {
  line-height: 26px;
}

.tree-ctrl {
  position: relative;
  cursor: pointer;
  color: $color-blue;
  margin-left: -$space-width;
}
</style>
