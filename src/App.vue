<template>
  <div id="se" @mousedown.stop="handleBox">
    <el-checkbox-group id="ul" v-model="selectList" >
      <el-checkbox
        v-for="(item, index) in list"
        :key="index"
        @click.stop="handleClick(index)"
        :label="index"
        :checked="item.isS"
        class="li"
      >
<br>
      </el-checkbox>
    </el-checkbox-group>

    <div v-show="isShowSeBox" id="selection"></div>
  </div>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      list: [
        {
          id: 1,
          isS: false,
        },
        {
          id: 2,
          isS: false,
        },
        {
          id: 3,
          isS: false,
        },
        {
          id: 4,
          isS: false,
        },
        {
          id: 5,
          isS: false,
        },
        {
          id: 6,
          isS: false,
        },
        {
          id: 7,
          isS: false,
        },
        {
          id: 8,
          isS: false,
        },
        {
          id: 9,
          isS: false,
        },
        {
          id: 10,
          isS: false,
        },
        {
          id: 11,
          isS: false,
        },
        {
          id: 12,
          isS: false,
        },
        {
          id: 13,
          isS: false,
        },
        {
          id: 14,
          isS: false,
        },
        {
          id: 15,
          isS: false,
        },
        {
          id: 16,
          isS: false,
        },
        {
          id: 17,
          isS: false,
        },
        {
          id: 18,
          isS: false,
        },
      ],
      isshift: false, // 是否按下 shift
      isctrl: false, // 是否按下 ctrl
      isonkeydown: false, // 是否按下键盘
      selectBox: null, // 框选盒子
      isShowSeBox: false, // 框选是否显示div
      selectList: [], // 每次点击，push进去点击的index
      lipL: 0, // 框选li的父盒子针对于窗口的left
      lipT: 0, // 框选li的父盒子针对于窗口的top
    }
  },
  mounted() {
    this.selectBox = document.getElementById('selection')
    this.parentBox = document.getElementById('se')
    this.handleScroll()
  },
  computed: {},
  watch: {},
  methods: {
    handleBox(even) {
      if (!this.isctrl && !this.isshift && this.selectList.length != 0) {
        this.selectList = []
        this.loop()
      }
      // 按下时的坐标
      this.downX = even.clientX
      this.downY = even.clientY
      this.selectBox.style.left = this.downX + 'px'
      this.selectBox.style.top = this.downY + 'px'
      document.body.addEventListener('mousemove', this.moveselecttion)
      document.body.addEventListener('mouseup', this.mouseupSelection)
      window.addEventListener('scroll', this.handleScroll)
      window.addEventListener('keydown', this.clickKeydown)
      window.addEventListener('keyup', this.clickKeyup)
    },
    // 页面滚动  滚动时，实时更新父盒子横纵坐标
    handleScroll() {
      //获取li父元素的纵坐标（相对于窗口）
      this.lipT = this.parentBox.getBoundingClientRect().top
      //获取li父元素的横坐标（相对于窗口）
      this.lipL = this.parentBox.getBoundingClientRect().left
    },
    // 键盘按下
    clickKeydown(e) {
      switch (e.keyCode) {
        case 16:
          this.isshift = true
          break
        case 17: // window 键盘
          this.isctrl = true
          break
        case 91: // mac command  按键
          this.isctrl = true
          break
      }
    },
    // 键盘松开
    clickKeyup(e) {
      this.isshift = false
      this.isctrl = false
    },
  

    // 鼠标移动事件
    moveselecttion(even) {
      this.isShowSeBox = true
      this.selectBox.style.left = Math.min(even.clientX, this.downX) + 'px'
      this.selectBox.style.top = Math.min(even.clientY, this.downY) + 'px'
      this.selectBox.style.width = Math.abs(this.downX - even.clientX) + 'px'
      this.selectBox.style.height = Math.abs(this.downY - even.clientY) + 'px'
      this.covered()
    },
    // 鼠标松开事件
    mouseupSelection(even) {
      this.isShowSeBox = false
      this.selectBox.style.width = 0 + 'px'
      this.selectBox.style.height = 0 + 'px'
      document.body.removeEventListener('mousemove', this.moveselecttion)
      document.body.removeEventListener('mouseup', this.mouseupSelection)
    },
    // 是否被覆盖
    covered() {
      let li = document.getElementsByClassName('li')
      if (!this.isctrl && !this.isctrl) {
        this.selectList = []
      }
      let l = parseInt(this.selectBox.style.left)
      let t = parseInt(this.selectBox.style.top)
      let w = parseInt(this.selectBox.style.width)
      let h = parseInt(this.selectBox.style.height)
      for (let i = 0; i < li.length; i++) {
        if (this.selectList.indexOf(i) == -1) {
          //  子元素是相对父元素定位的，所以父元素有定位高度，offsetLeft 和 offsetTop不准
          let sl = li[i].offsetWidth + li[i].offsetLeft + this.lipL
          let st = li[i].offsetHeight + li[i].offsetTop + this.lipT
          if (
            sl > l &&
            st > t &&
            li[i].offsetLeft + this.lipL < l + w &&
            li[i].offsetTop + this.lipT < t + h
          ) {
            this.selectList.push(i)
          }
          this.loop()
        }
      }
    },
    // 循环遍历,在这里，可以拿到所选的item
    loop() {
      this.list.forEach((e, index) => {
        e.isS = false
        this.selectList.filter((item) => {
          if (item == index) {
            e.isS = true
          }
        })
      })
    },
  },
  destroyed() {
    document.body.removeEventListener('mousemove', this.moveselecttion)
    document.body.removeEventListener('mouseup', this.mouseupSelection)
    window.removeEventListener('scroll', this.handleScroll)
    window.removeEventListener('keydown', this.clickKeydown)
    window.removeEventListener('keyup', this.clickKeyup)
  },
}
</script>
<style lang='less' scoped>
#se {
  position: relative;
  width: 100%;
  height: 100%;
  -moz-user-select: none; /*火狐*/
  -webkit-user-select: none; /*webkit浏览器*/
  -ms-user-select: none; /*IE10*/
  -khtml-user-select: none; /*早期浏览器*/
  user-select: none;
}
#ul {
  display: flex;
  flex-wrap: wrap;
  width: 350px;
  margin: 100px auto;
  .li {
    box-sizing: content-box;
    padding: 1px 1px;
    margin: 1px;
    cursor: pointer;
  }
  
// 多选
/deep/ .el-checkbox__inner{
    width: 40px;
    height: 40px;
  border: 2px solid #DCDFE6;
  border-radius: 20%;
}
}
#selection {
  position: fixed;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background: linear-gradient(
    to top left,
    rgba(229, 222, 228, 0.5),
    rgba(232, 230, 223, 0.2)
  );
  border: 1px dotted slategray;
  color: rgb(36, 138, 129);
  font-size: 16px;
  width: 0px;
  height: 0px;
  overflow: hidden;
}
</style>