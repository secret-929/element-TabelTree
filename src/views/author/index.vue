<template>
  <div class="app-container">
    <tree-table
      :data="data"
      :columns="columns"
      border
      @getAuth="getAuth"
    ></tree-table>
  </div>
</template>

<script>
// :expandAll="false"

/**
  Auth: Leiy
  Created: 2018/09/29-11:25
  Explain:根据花裤衩的表格改的,
  isIndeterminate属性是控制多选半选中状态，
  checkAll是控制全选中状态
  selectchecked是放置sonData1选中项
*/
import treeTable from '@/components/TreeTableAuthor'

export default {
  name: 'treeTableDemo',
  components: { treeTable },
  data() {
    return {
      columns: [
        {
          text: '菜单列表',
          value: 'description',
          width: 300,
          option: 'sonData1'
        },
        {
          text: '功能权限',
          value: 'sonData1',
          option: 'sonData1'
        }
      ],
      data: [
        {
          type: 0,
          'checked': false,
          'id': '1',
          'description': '用户管理',
          isIndeterminate: false,
          checkAll: false,
          selectchecked: ['11'],
          'sonData1': [
            {
              type: 2,
              'description': '啦啦啦啦',
              'parentId': '1',
              'checked': false,
              'id': '11'

            },
          ],
          children: [
            {
              type: 1,
              id: 6,
              'description': '用户列表',
              'parentId': '-1',
              'checked': false,
              selectchecked: ['7', '9'],
              checkAll: false,
              isIndeterminate: false,
              'sonData1': [
                {
                  type: 2,
                  'description': '用户新增',
                  'parentId': '6',
                  'checked': false,
                  'id': '7'

                },
                {
                  type: 2,
                  'description': '用户修改',
                  'parentId': '6',
                  'checked': false,
                  'id': '8'
                },
                {
                  type: 2,
                  'description': '用户删除',
                  'parentId': '6',
                  'checked': false,
                  'id': '9'
                }
              ],
              children: [
                {
                  type: 1,
                  id: 16,
                  'description': '测试1',
                  'parentId': '6',
                  'checked': false,
                  selectchecked: [],
                  checkAll: false,
                  isIndeterminate: false,
                  'sonData1': [
                    {
                      type: 2,
                      'description': '测试权限111',
                      'parentId': '16',
                      'checked': false,
                      'id': '161'
                    },
                    {
                      type: 2,
                      'description': '测试权限222',
                      'parentId': '16',
                      'checked': false,
                      'id': '162'
                    }
                  ],
                  children: [
                    {
                      type: 1,
                      id: 22,
                      'description': '阀手动阀',
                      'parentId': '16',
                      'checked': false,
                      selectchecked: ['221', '222'],
                      checkAll: false,
                      isIndeterminate: false,
                      'sonData1': [
                        {
                          'description': '阀手动阀搭啥',
                          'parentId': '22',
                          'checked': false,
                          'id': '221'
                        },
                        {
                          'description': 'aaaaaaaaa',
                          'parentId': '22',
                          'checked': false,
                          'id': '222'
                        }
                      ],
                      children: [
                        {
                          type: 1,
                          id: 333,
                          'description': 'cccccc',
                          'parentId': '22',
                          'checked': false,
                          selectchecked: ['3335'],
                          checkAll: false,
                          isIndeterminate: false,
                          'sonData1': [
                            {
                              'description': 'zzzzzzzz',
                              'parentId': '333',
                              'checked': false,
                              'id': '3334'
                            },
                            {
                              'description': 'aaaaaaaaa',
                              'parentId': '333',
                              'checked': false,
                              'id': '3335'
                            }
                          ]
                        }],
                    }],
                },
                {
                  type: 1,
                  id: 17,
                  'description': '测试2',
                  'parentId': '-1',
                  'checked': false,
                  selectchecked: [],
                  checkAll: false,
                  isIndeterminate: false,
                  'sonData1': [

                  ]
                },
                {
                  type: 1,
                  id: 18,
                  'description': '测试3',
                  'parentId': '-1',
                  'checked': false,
                  selectchecked: [],
                  checkAll: false,
                  isIndeterminate: false,
                  'sonData1': [{
                    type: 2,
                    'description': '测试权限333',
                    'parentId': '17',
                    'checked': false,
                    'id': '171'
                  }, {
                    type: 2,
                    'description': '测试权限444',
                    'parentId': '17',
                    'checked': false,
                    'id': '172'
                  }
                  ]
                }
              ]
            },
            {
              type: 1,
              id: 13,
              'description': '角色列表',
              'parentId': '-1',
              'checked': false,
              selectchecked: [],
              checkAll: false,
              isIndeterminate: false,
              'sonData1': [
                {
                  type: 2,
                  'description': '角色授权',
                  'parentId': '6',
                  'checked': false,
                  'id': '10'

                },
                {
                  type: 2,
                  'description': '角色修改',
                  'parentId': '6',
                  'checked': false,
                  'id': '11'
                },
                {
                  type: 2,
                  'description': '角色删除',
                  'parentId': '6',
                  'checked': false,
                  'id': '12'
                }
              ]
            }
          ]
        },
        {
          type: 0,
          'checked': false,
          'id': '2',
          'description': '设备管理',
          isIndeterminate: false,
          checkAll: false,
          selectchecked: [],
          'sonData1': [],
          children: [
            {
              type: 1,
              id: 6,
              'description': '设备列表',
              'parentId': '-1',
              'checked': false,
              selectchecked: [],
              checkAll: false,
              isIndeterminate: false,
              'sonData1': [
                {
                  type: 2,
                  'description': '设备新增',
                  'parentId': '6',
                  'checked': false,
                  'id': '17'

                },
                {
                  type: 2,
                  'description': '设备修改',
                  'parentId': '6',
                  'checked': false,
                  'id': '18'
                },
                {
                  type: 2,
                  'description': '设备删除',
                  'parentId': '6',
                  'checked': false,
                  'id': '19'
                }
              ]
            }
          ]
        }
      ]
    }
  },
  created() {

  },
  methods: {
    getAuth(data) {
      console.log(data)
      let opt = []
      data.forEach(val => {
        opt.push(val.id)
        if (val.children) {
          val.children.forEach(el => {
            if (el.selectchecked.length) {
              opt.push(el.id)
              opt.push(el.selectchecked)
            }
          })
        }
      })
      // opt = opt.join().split(',').filter(n => { return n })
      // console.log(opt)
    }
  }
}
</script>
