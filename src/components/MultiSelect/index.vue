<!-- eslint-disable no-tabs -->
<template>
	<div class="multi-select">
		<el-select v-model="value" multiple placeholder="Select">
			<el-tabs v-model="activeName" @tab-click="handleClick">
				<el-tab-pane v-for="tab in tabNames" :key="tab" :label="tab" :name="tab">
				</el-tab-pane>
			</el-tabs>

			<div style="display: inline-flex">
				<div v-if="['group', 'dept', 'role'].includes(activeName)" class="drop_tl" @mouseenter="showFilter = true" @mouseleave="showFilter = false">
					<span>选择类别 <i class="el-select__caret el-input__icon el-icon-arrow-up is-reverse"></i></span>
					<ul v-show="showFilter" class="drop_l">
						<li v-for="group in showFilterList" :key="group.name" @click.stop.prevent="handleSelectGroup(group)" >{{ group.name }}</li>
					</ul>
				</div>

				<el-input v-model="keyword" class="">
					<i slot="prefix" class="el-input__icon el-icon-search" />
				</el-input>
			</div>

			<div class="list-data">
				<el-tree
				v-if="['group', 'dept', 'role'].includes(activeName)"
				:props="treeProps" :data="treeData"
				:filter-node-method="filterNode"
				ref="tree"
				@node-click="handleNodeClick">
				<li class="custom-tree-node el-select-dropdown__item" slot-scope="{ node, data }" :class="{ selected: data.userId && value.includes(data.userId)}">
					<span>{{ node.label }}</span>
				</li>
				</el-tree>
				<div v-else>
					<div v-if="filterList.length === 0 && keyword"> No data Match</div>
					<div v-else>
						<li class="el-select-dropdown__item" :class="{ selected: value.includes(item.userId)}" v-for="item in filterList" :key="item.name" @click.stop.prevent="selectedFromList(item.userId)">
							<span>
								{{ item.name }}
							</span>
						</li>
					</div>
				</div>
			</div>

			<div
				v-show="activeName === tabNames[0] && !keyword">
			<el-option
				v-for="item in list"
				:key="item.userId"
				:label="item.name"
				:value="item.userId">
			</el-option>
			</div>

			<div class="block">
				<el-pagination
					@current-change="changePage"
					:page-size="3"
					small
					layout="prev, pager, next"
					:total="list.length">
				</el-pagination>
			</div>

  </el-select>
  </div>
</template>

<script>
import { mockData } from '../../../data'

export default {
  name: 'HelloWorld',
  data () {
    return {
      value: [],
      activeName: 'All',
      tabNames: ['All', 'group', 'dept', 'role'],
      keyword: '',
      list: [],
      filterList: [],
      options: mockData,
      treeGroupDate: [],
      treeDeptDate: [],
      treeRoleDate: [],
      treeData: [],
      filterTreeData: [],
      treeProps: {
        label: 'name',
        children: 'children'
      },
      showFilter: false,
      showFilterList: []
    }
  },
  watch: {
    activeName (val) {
      switch (val) {
        case this.tabNames[1] :
          this.treeData = this.treeGroupDate
          this.showFilterList = this.treeGroupDate
          return
        case this.tabNames[2]:
          this.treeData = this.treeDeptDate
          this.showFilterList = this.treeDeptDate
          return
        case this.tabNames[3]:
          this.treeData = this.treeRoleDate
          this.showFilterList = this.treeRoleDate
          return
        default:
          this.list = this.options
      }
    },

    keyword (val) {
      this.filterList = val ? this.list.filter(user => user.name.indexOf(val) !== -1) : []
      if (this.$refs.tree) this.$refs.tree.filter(val)
    },

    filterTreeData (val) {
      if (val && val.length) this.treeData = val
    }
  },
  methods: {
    handleClick (tab, event) {
      this.filterTreeData = []
			this.keyword = ''
    },

    handleNodeClick (data) {
      if (data.userId) this.selectedFromList(data.userId)
    },

    // filter Node
    filterNode (value, data) {
      if (!value) return true
      return data.name.indexOf(value) !== -1
    },

    // handleSelectGroup
    handleSelectGroup (data) {
      this.filterTreeData = [data]
      this.showFilter = false
    },

    // slected from the list
    selectedFromList (id) {
      if (!this.value.includes(id)) {
        this.value.push(id)
      } else {
        this.value = this.value.filter((ids) => ids !== id)
      }
    },

    changePage (page) {
      console.log(page)
    },
    getRandomChoice (quotes) {
      return quotes[Math.floor(Math.random() * quotes.length)]
    },

    makeGroupData (filterStr) {
      const groups = [1, 2, 3, 4]
      return groups.reduce((acc, crr) => {
        const newArr = mockData.filter((user) => user[`${filterStr}`] === crr)
        const newObject = {
          name: `${filterStr}${crr}`,
          children: newArr
        }
        // console.log(newObject, acc)

        return [...acc, {...newObject}]
        // acc.push(newObject)
      }, [])
      // console.log('the>>>>>>', groupData)
    },
    getAllTreeData () {
      this.list = this.options
      this.treeGroupDate = this.makeGroupData('group')
      this.treeDeptDate = this.makeGroupData('dept')
      this.treeRoleDate = this.makeGroupData('role')
    }
    // to create a mock data
    // makeMockData () {
    //   const fakeData = []
    //   const groups = [1, 2, 3, 4]
    //   const names = ['小张', '小刘', '小马', '小韩']

    //   for (let i = 0; i < 120; i++) {
    //     let u = {
    //       name: `${this.getRandomChoice(names) + i}`,
    //       group: this.getRandomChoice(groups),
    //       dept: this.getRandomChoice(groups),
    //       userId: 'user' + i
    //     }
    //     fakeData.push(u)
    //   }
    //   console.log('fakeData', JSON.stringify(fakeData))
    // }

  },
  mounted () {
    this.getAllTreeData()
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
/* el-select-dropdown__item selected */
.multi-select {
}
.drop_tl {
	position: relative;
	font-size: 12px;
	padding-right: 8px;
}

.drop_tl:hover {
	.el-select__caret.el-input__icon.el-icon-arrow-up.is-reverse {
		rotate: 180deg;
	}
}
.drop_l {
	position: absolute;
	top: 34px;
	right: 33px;
	font-size: 12px;
	z-index: 1;
	background: #e5e9ec;
	li {
		font-size: 14px;
    padding: 2px;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    color: #606266;
    -webkit-box-sizing: border-box;
    box-sizing: border-box;
    cursor: pointer;
	}
	li:hover {
		background-color: #F5F7FA;
	}
}

.el-input.el-input--prefix {
	flex: 1;
	.el-input__inner {
		height: 34px;
	}
	.el-input__prefix .el-input__icon {
		line-height: 34px;
	}
}

.custom-tree-node {
	width: 100%;
	height: 100%;
	padding: 0;
}
.custom-tree-node.selected.el-select-dropdown__item {
	background: transparent;
	height: 100%;
}
.block {
	/* position: absolute; */
	bottom: 0;
	width: 100%;
}
</style>
