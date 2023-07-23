---
title: vue2开发楼层滚动交互
date: 2023-07-23 17:28:31
categories: 前端
tags: vue
excerpt: 楼层导航和页面滚动监听……
---

## 业务需求

根据接口返回的一个list，渲染成一个长表单页面，item中含楼层信息，不确定每层多少个项，实现页面滚动和楼层导航的联动交互。

记得上次写这个样的长表单滚动交互还是N久之前用jq写的。

## 实现的原理

实现点击导航滚动
[element.scrollTo()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTo)

监听页面滚动激活对应的导航
window.addEventListener("scroll", handleScroll)

渲染页面时候添加ref，使用element.offsetTop获取元素的偏移位置

## 实现代码

```js
// ScrollPage.vue
<template>
  <div>
    <!-- 导航内容 -->
    <div class="task-tag-list-container">
      <ul class="task-tag-list">
        <li
          v-for="item in taskTagList"
          :key="item.value"
          @click="changeTaskTag(item)"
          :class="{
            'task-tag-item': true,
            active: item.value === currentTagItem
          }"
        >{{item.label }}</li>
      </ul>
    </div>
    <!-- 滚动内容 -->
    <div class="task-detail" ref="taskDetailContainerRef">
      <div v-for="(taskItem, TaskIndex) in list" :key="taskItem.id">
        <div
          class="building-name"
          :ref="taskItem.storey + 'ref'"
          v-if="TaskIndex == 0 || list[TaskIndex - 1].storey != list[TaskIndex].storey"
        >{{ taskItem.storey }}</div>
        <div class="task-detail-item-container">内容，有交互，不定高度</div>
      </div>
    </div>
  </div>
</template>
<script>
import { throttle } from "../utils/index.js";
export default {
  name: "ScrollPage",
  data() {
    return {
      currentTagItem: "楼层",
      currentTagNextItem: "",
      currentTagPrevItem: "",
      list: [
        {
          id: 1,
          storey: "楼层"
        },
        {
          id: 2,
          storey: "2楼"
        },
        {
          id: 3,
          storey: "2楼"
        },
        {
          id: 4,
          storey: "5楼"
        },
        {
          id: 5,
          storey: "5楼"
        },
        {
          id: 6,
          storey: "5楼"
        },
        {
          id: 7,
          storey: "8楼"
        },
        {
          id: 8,
          storey: "9楼"
        },
        {
          id: 9,
          storey: "9楼"
        },
        {
          id: 10,
          storey: "9楼"
        },
        {
          id: 11,
          storey: "9楼"
        },
        {
          id: 12,
          storey: "9楼"
        }
      ]
    };
  },
  computed: {
    taskTagList() {
      let cloneData = JSON.parse(JSON.stringify(this.list));
      cloneData = cloneData.map(father => {
        return father.storey;
      });
      cloneData = Array.from(new Set(cloneData));
      return cloneData.map(item => {
        return {
          value: item,
          label: item
        };
      });
    }
  },
  mounted() {
    // 监听页面滚动，加throttle防止触发交互太频繁
    window.addEventListener("scroll", throttle(this.handleScroll, 300));
  },
  beforeDestroy() {
    window.removeEventListener("scroll", throttle(this.handleScroll, 300));
  },
  methods: {
    handleScroll() {
      const scrollTop = window.scrollY;
      // 处理滚动位置
      let val = this.taskTagList;

      if (Array.isArray(val)) {
        let nextTagValue = val[val.length - 1].value;
        let prevTagValue = val[0].value;
        val.forEach((tagItem, index) => {
          if (tagItem.value == this.currentTagItem && index < val.length - 1) {
            nextTagValue = val[index + 1].value;
          }
          if (tagItem.value == this.currentTagItem && index > 0) {
            prevTagValue = val[index - 1].value;
          }
        });
        this.currentTagNextItem = nextTagValue;
        this.currentTagPrevItem = prevTagValue;
      }
      if (
        scrollTop + 90 >
        this.$refs[this.currentTagNextItem + "ref"][0].offsetTop
      ) {
        this.currentTagItem = this.currentTagNextItem;
      } else {
        if (
          scrollTop + 90 <
          this.$refs[this.currentTagItem + "ref"][0].offsetTop
        ) {
          this.currentTagItem = this.currentTagPrevItem;
          console.log("this.currentTagItem", this.currentTagItem);
        }
      }
    },
    changeTaskTag(item) {
      this.currentTagItem = item.value;
      console.log(this.$refs[item.value + "ref"][0].offsetTop);
      window.scrollTo({
        top: this.$refs[item.value + "ref"][0].offsetTop - 90,
        behavior: "smooth"
      });
    }
  }
};
</script>

<style scoped lang="scss">
.task-tag-list-container {
  position: sticky;
  top: 0;
  left: 0;
  z-index: 5;
  background-color: #fff;
  border-bottom: 1px solid #a3a3a3;
  .task-tag-list {
    display: flex;
    padding: 16px;
    > li {
      list-style-type: none;
      margin: 8px;
      font-size: 16px;
    }

    .active {
      font-weight: bold;
      color: #2a2b32;
      &::after {
        content: "";
        display: block;
        width: 18px;
        height: 4px;
        border-radius: 3px;
        background-color: #ffd333;
        margin: 0 auto;
        margin-top: 5px;
      }
    }
  }
}
.task-detail {
  padding: 16px;
  .building-name {
    margin-top: 16px;
    margin-bottom: 16px;
  }
  .task-detail-item-container {
    margin-top: 16px;
    height: 200px;
    border-radius: 8px;
    border: 1px solid #a3a3a3;
    padding: 16px;
  }
}
</style>
```