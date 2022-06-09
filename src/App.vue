<template>
  <div id="app">
    <div id="drag-area">.</div>
    <div id="container">
      <div id="drawer-btn" @click="toggleDrawer()">{{ drawerOpen ? "Open Gallery" : "Close Gallery" }}</div>
      <div  v-for="(item, key) in draggableItems" :key="key" class="item">
        <div v-if="item.type == '1'" class="leaf" v-draggable="draggableValue" v-bind="{ 'proto': item.proto }" :style="{'background': item.color, 'transform': 'scale('+item.scale+')'}" :data-showmenu="item.edit" :id="item.id" @dblclick="openMenu(item.id, item.proto, $event)">
        </div>
        <div v-if="item.type == '2'" class="square" v-draggable="draggableValue" v-bind="{ 'proto': item.proto }" :style="{'background': item.color, 'transform': 'scale('+item.scale+')'}" :data-showmenu="item.edit" :id="item.id" @dblclick="openMenu(item.id, item.proto, $event)">
        </div>
        <div v-if="item.type == '3'" class="circle" v-draggable="draggableValue" v-bind="{ 'proto': item.proto }" :style="{'background': item.color, 'transform': 'scale('+item.scale+')'}" :data-showmenu="item.edit" :id="item.id" @dblclick="openMenu(item.id, item.proto, $event)">
        </div>
      </div>
    </div>
    <div ref="menu" class="menu">
        <p class="close-btn"  @click="closeMenu()">X</p>
        <div class="editor">
          <p>Color:</p>
          <input type="text" @change="changeColor($event)" :value="this.draggableItems[this.activeIndex].color">
        </div>
        <div class="editor">
          <p>Scale:</p>
          <input type="text"  @change="changeScale($event)" :value="this.draggableItems[this.activeIndex].scale">
        </div> 
      </div>
  </div>
</template>

<script>
import { Draggable } from 'draggable-vue-directive'

export default {
  name: 'App',
  components: {
  },
   directives: {
      Draggable,
  },
  data() {
      return {
        activeIndex: 0,
        cloneFlag: false,
        drawerOpen: true,
        draggableValue: {    
          onPositionChange: this.pushArray,
          onDragEnd: this.reset,
          boundingRect: new DOMRect(20,20,window.innerWidth,window.innerHeight-40)
        },             
        draggableItems:  [
          { 
            id:0,
            type: "1",
            proto: true,
            color: "#35b997",
            scale: "1",
          },
          { 
            id:1,
            type: "2",
            proto: true,
            color: "#f05561",
            scale: "1",
          },
          { 
            id:2,
            type: "3",
            proto: true,
            color: "#fee851",
            scale: "1",
          },
        ] 
      };
  },
  methods: {    

    toggleDrawer() {
      let drawer = document.getElementById("container")
      let dragArea = document.getElementById("drag-area")
      let drawerBtn = document.getElementById("drawer-btn")
      if (this.drawerOpen == false) {
        drawer.style.right = "-200px"
        dragArea.style.width = "100%"
        drawerBtn.style.right = "0px"
        this.drawerOpen = true
      } else {
        drawer.style.right = "20px"
        dragArea.style.width = "calc(100% - 150px)"
        drawerBtn.style.right = "170px"
        this.drawerOpen = false
      }

    },
    changeColor(e) {
      let item = document.getElementById(this.activeIndex)
      let color = e.srcElement.value
      
      if (color != "") {
        item.style.background = color
        item.value = color
      } else {
        item.style.background = this.draggableItems[this.activeIndex].color
      }
    },
    changeScale(e) {
      let item = document.getElementById(this.activeIndex)
      let val = e.srcElement.value
      
      if (val != "") {
        item.style.transform = "scale("+val+")"
      } else {
        item.style.transform = "scale("+this.draggableItems[this.activeIndex].scale+")" 
      }
    }, 
    reset(){
      this.cloneFlag = false
    },
    pushArray(positionDiff, absolutePosition, event) {
        if(event){
          let proto = event.target.getAttribute("proto") == "true"
        
        if(!this.cloneFlag && proto){
          event.target.setAttribute("proto",false) 
          this.draggableItems.push(Object.assign({},this.draggableItems[event.target.id]))        
          let newele = this.draggableItems[this.draggableItems.length-1]
          this.draggableItems[event.target.id].proto = false;
          newele.id = this.draggableItems.length - 1
          this.cloneFlag = true
        }
        this.closeMenu()
      }
    },
    openMenu(id, proto, e) {
      this.closeMenu()
      this.activeIndex = id;

      if (proto == false) {
        this.$refs.menu.style.display ="block"
        this.$refs.menu.style.position ="absolute"
        this.$refs.menu.style.left = (e.pageX+90)+'px'
        this.$refs.menu.style.top = (e.pageY-70)+'px'
      }
    },
    closeMenu() {
      this.$refs.menu.style.display ="none"
    },
  },
}
</script>

<style>
body {
  margin: 0;
  overflow: hidden;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
}
#app {
  padding: 20px;
  background-color: #092532;
  transition: 0.5s all;
}
#drag-area {
  width: 100%;
  background: #FAF9F6;
  height: calc(100vh - 40px);
  color: #fff;
  transition: 0.5s all;
}
#container {
  width: 120px;
  margin-left: auto;
  top: 0;
  position: fixed;
  right: 20px;
  height: 100vh;
  right: -150px;
  transition: 0.5s all;
}
#drawer-btn  {
  position: fixed;
  right: 20px;
  top: 20px;
  background: #092532;
  color: #fff;
  padding: 10px 15px;
  text-align: center;
  transition: 0.5s all;
  font-weight: 700;
  border-radius: 0 0 0 12px;
  cursor: pointer;
}
.menu{
  display: none;
  padding: 0 20px 20px 20px;
  border-radius: 4px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.12), 0 1px 2px rgba(0,0,0,0.24);
  transition: all 0.3s cubic-bezier(.25,.8,.25,1);
}
.close-btn {
  cursor: pointer;
  text-align: right;
}
.item {
  display: flex; 
  justify-content: center;
  cursor: pointer;
  transition: transform 0.5s;
}
.circle {
  position: fixed;
  border-radius: 50%;
  width: 100px;
  height: 100px;
  top: 100px;
}
.square {
  position: fixed;
  width: 100px;
  height: 100px;
  top: 250px;
}
.leaf {
  position: fixed;
  width: 100px;
  height: 100px;
  border-radius: 50% 10%;
  top: 410px;
}
input {
  width: 80px
}
.text-dark {
  color: #333333;
}
</style>
