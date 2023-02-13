<template>
  <div class="stack w-100">
    <div class="stack button cp" ref="left" @click="scrollLeft" v-if="showBtn"><YfIcon iconName="jiantou_liebiaoxiangzuo_o" :size="24"></YfIcon></div>
    <div class="stack w-100 tab" ref="tab">
      <template v-for="(item,index) in tabs" :key="index">
        <div :class="['tab-item', 'px-5', 'cp', 'stack', 'stack-center', { 'active': modelValue === index }]" @click.stop="() => handleClick(index, item)" ref="tabItem">
          <slot name="header" :tabs="item">{{ item }}</slot>
        </div>
      </template>
    </div>
    <div class="stack button cp" type="text" ref="right" @click="scrollRight" v-if="showBtn"><YfIcon iconName="jiantou_liebiaoxiangyou_o" :size="24"></YfIcon></div>
  </div>
</template>

<script>
export default {
  name: 'tabHeader',
  props: {
    tabs: {
      type: Array,
    },
    modelValue: {
      type: Number,
      default: 0,
    },
  },
  data() {
    return {
      allWidth: 0,
      showBtn: false,
    }
  },
  methods: {
    handleClick(index, item) {
      this.$emit('update:modelValue', index);
      this.$emit('click', index, item);
      if (this.showBtn) {
        const { right: btnLeft } = this.$refs.left.getBoundingClientRect();
        const { left: btnRight } = this.$refs.right.getBoundingClientRect();
        const { left, right } = this.$refs.tabItem[index].getBoundingClientRect();
        if (left < btnRight && right > btnRight) {
          this.$refs.tab.scrollLeft += right - btnRight;
        } else if (left < btnLeft && right > btnLeft) {
          this.$refs.tab.scrollLeft -= btnLeft - left;
        }
      }
    },
    scrollLeft() {
      this.$nextTick(() => {
        this.$refs.tab.scrollLeft -= this.width;
      })
    },
    scrollRight() {
      this.$nextTick(() => {
        this.$refs.tab.scrollLeft += this.width;
      })
    },
  },
  mounted() {
    this.$refs.tabItem.forEach(o => {
      this.allWidth += o.offsetWidth;
    });
    try {
      const observer = new ResizeObserver(() => {
        this.width = this.$refs.tab.clientWidth;
        this.showBtn = this.width < this.allWidth;
      });
      observer.observe(this.$el);
    } catch (err) {
      this.width = this.$refs.tab.clientWidth;
    }
},
}
</script>

<style lang="less" scoped>
.stack {
  display: flex;
  align-items: center;
}
.stack-center {
  justify-content: center;
}
.w-100 {
  width: 100%;
}
.button {
  height: 100%;
  width: 30px;
}
.cp {
  cursor: pointer;
}

.tab {
  overflow-x: auto;
  .tab-item {
    height: 40px;
    white-space: nowrap;
    position: relative;
    cursor: pointer;
  }
  .px-5 {
    padding: 0 20px;
  }
  .tab-item::after {
    content: '';
    position: absolute;
    width: 100%;
    left: 0;
    bottom: 0;
    height: 2px;
    transform: scale(0);
    transform-origin: center;
    transition: transform 200ms;
  }
  .active {
    transition: 200ms;
    color: #1890ff;
  }
  .active::after {
    transform: scale(1);
    background: #1890ff;
  }
}
::-webkit-scrollbar {
  display: none;
}
</style>
