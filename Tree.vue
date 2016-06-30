<template>
<div v-for="item in model" class="tree-ul" >
  <div class="tree-content">
    <div :id="'item_id_' + item.id" :class="{'item-text': true}" v-bind:style="{paddingLeft: marginLeft+'px'}" @click="itemClick(item, $event)" onselectstart="return false;">

    <span :class="{'item-icon':true, 'arrow-close': item.children.length > 0 && !item.expanded, 'arrow-open': item.children.length > 0 && item.expanded}" @click.stop="expandClick(item)"></span>

    <span :class="{'item-icon':true,'tree-leaf':item.children.length == 0,
    'tree-folder-close': item.children.length > 0 && !item.expanded,
    'tree-folder-open': item.children.length > 0 && item.expanded}"></span>

    <span>{{item.text}}</span>
    </div>
    <div class="children-ul" v-show="item.expanded" transition="expand" v-if="item.children.length">
      <!-- 组件自己调用自己 -->
      <tree :model="item.children" :select-id.sync="selectId" :margin-left="marginLeft" :tree-click="treeClick"></tree>
    </div>
  </div>
</div>

</template>

<script>
export default {
  data () {
    return {
      open: false,
      count: 0,
      timer: null
    }
  },
  name: 'tree',
  props: {
    model: {
      type: Array,
      default: []
    },
    treeClick: {
      type: Function,
      default: function () { }
    },
    marginLeft: {
      type: Number,
      default: -15
    },
    selectId: {
      type: String,
      default: '',
      twoWay: true
    }
  },
  created () {
    this.marginLeft = this.marginLeft + 15
  },
  methods: {
    itemClick (item) {
      // 罪过罪过 还是用DOM操作了
      // 设置选择项背景颜色
      let selected = document.getElementsByClassName('item-text-bg')[0]
      if (selected) {
        selected.className = selected.className.replace('item-text-bg', '')
      }
      let cSelect = document.getElementById('item_id_' + item.id)
      cSelect.className = cSelect.className + ' item-text-bg'

      // 单双击事件处理
      this.count++
      let me = this
      this.timer = window.setTimeout(function () {
        if (me.count === 1) {
          // 回调函数
          me.treeClick(item)
          me.selectId = item.id
        } else {
          if (item.children.length > 0) {
            item.expanded = !item.expanded
          }
        }
        window.clearTimeout(me.timer)
        me.count = 0
      }, 200)
    },
    expandClick (item) {
      if (item.children.length > 0) {
        item.expanded = !item.expanded
      }
    }
  }
}
</script>

<style lang="less">
.tree-ul {
  text-align: left;
  float: left;
  cursor: default;
  width: 100%;
  .tree-folder-close {
    background-image: url(../assets/tree-folder-close.png);
    background-repeat: no-repeat;
    background-position: 2px 2px;
  }

  .tree-folder-open {
    background-image: url(../assets/tree-folder-open.png);
    background-repeat: no-repeat;
    background-position: 2px 2px;
  }

  .arrow-close {
    background-image: url(../assets/arrows.png);
    background-repeat: no-repeat;
    background-position: 3px -2px;
  }

  .arrow-open {
    background-image: url(../assets/arrows.png);
    background-repeat: no-repeat;
    background-position: -14px -2px;
  }

  .tree-leaf {
    background-image: url(../assets/tree-leaf.png);
    background-repeat: no-repeat;
    background-position: 2px 2px;
  }

  .item-icon {
    height: 20px;
    width: 20px;
    float: left;
  }

  .tree-content {
    float:left;
    width: 100%;
  }

  .children-ul {
    float: left;
    width: 100%;
  }

  .item-text {
    width: 100%;
    padding-top: 5px;
    padding-bottom: 5px;
  }

  .item-text:hover {
    background-color: #e2eff8;
  }

  .item-text-bg {
    background-color: #eaf2f4;
  }

  /**
   * 定义动画
   */
  .expand-transition {
    transition: all .2s ease;
    max-height: 9999px;
  }
  .expand-enter, .expand-leave {
    max-height: 0;
    opacity: 0;
  }

}

</style>
