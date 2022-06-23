<template>
  <div>
    <div class="title-contr">
      <h1>Vuejs Shapes Sandbox</h1>
    </div>
    <div :class="dragging" class="dragging" id="dragging"></div>
    <div class="editing"></div>
    <div class="container" id="container">
      <div v-if="moving"><h2 class="moving-hint-h2">You can use keyboard arrows to move the shape</h2></div>
    </div>
    <div class="footer">
      <h2>Drag a shape to the field</h2>
      <div v-if="dragging !== 'square'" class="square" @mousedown="drag('square')"></div>
      <div v-if="dragging !== 'triangle'" class="triangle" @mousedown="drag('triangle')"></div>
      <div v-if="dragging !== 'circle'" class="circle" @mousedown="drag('circle')"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'app',
  mounted() {},
  methods: {
    drag(shape) {
      if (!this.editing && !this.moving) {
        this.dragging = shape;
        this.currentShape = shape;
        if (this.eventsListenersOn === null) {
          const draggingDiv = document.getElementById('dragging');
          document.addEventListener('mousemove', (event) => {
            if (this.currentShape === 'triangle') {
              draggingDiv.style.transform = `translateY(${event.clientY - 25}px) translateX(${event.clientX - 40}px)`;
            } else {
              draggingDiv.style.transform = `translateY(${event.clientY - 20}px) translateX(${event.clientX - 20}px)`;
            }
          }, false);
          const container = document.getElementById('container');
          container.addEventListener('mouseup', (event) => {
            if (this.dragging) {
              this.mouseCoordinatesDefine(event);
              this.drawShape();
            }
          }, false);
          document.addEventListener('mouseup', () => {
            this.dragging = null;
          }, false);
          this.eventsListenersOn = 1;
        }
      }
    },
    mouseCoordinatesDefine(event) {
      this.mouseX = event.clientX;
      this.mouseY = event.clientY;
    },
    drawShape() {
      // creating shape
      const div = document.createElement('div');
      div.style.position = 'fixed';
      if (this.currentShape === 'triangle') {
        div.style.top = `${this.mouseY - 25}px`;
        div.style.left = `${this.mouseX - 40}px`;
      } else {
        div.style.top = `${this.mouseY - 20}px`;
        div.style.left = `${this.mouseX - 20}px`;
      }
      div.style.width = '40px';
      div.style.height = '40px';
      div.setAttribute('id', `shape-id-${this.shapeID}`);
      if (this.currentShape === 'square' || this.currentShape === 'triangle' || this.currentShape === 'circle') {
        div.classList.add(this.currentShape);
      }
      document.body.appendChild(div);
      // creating edit panel
      const divEdit = document.createElement('div');
      divEdit.setAttribute('id', `edit-panel-id-${this.shapeID}`);
      divEdit.setAttribute('class', 'edit-panel');
      const currentShapeTopPos = div.style.top.slice(0, -2);
      if (currentShapeTopPos < 163) {
        divEdit.style.top = '65px';
      } else {
        divEdit.style.top = '-160px';
      }
      div.appendChild(divEdit);
      // creating color selector
      const labelEditColor = document.createElement('label');
      labelEditColor.setAttribute('for', 'colorPicker');
      labelEditColor.innerText = 'Shape Color';
      divEdit.appendChild(labelEditColor);
      const inputEditColor = document.createElement('input');
      inputEditColor.setAttribute('type', 'color');
      inputEditColor.setAttribute('value', this.shapeBackgroundColor);
      inputEditColor.setAttribute('name', 'colorPicker');
      inputEditColor.setAttribute('id', 'colorPicker');
      divEdit.appendChild(inputEditColor);
      inputEditColor.addEventListener('change', () => {
        if (div.className === 'triangle') {
          div.style.borderBottomColor = inputEditColor.value;
        } else {
          div.style.backgroundColor = inputEditColor.value;
        }
      });
      // creating size selector
      const labelEditSize = document.createElement('label');
      labelEditSize.setAttribute('for', 'sizeEditor');
      labelEditSize.innerText = 'Shape Size';
      divEdit.appendChild(labelEditSize);
      const selectEditSize = document.createElement('select');
      selectEditSize.setAttribute('id', 'sizeEditor');
      divEdit.appendChild(selectEditSize);
      this.shapeSizes.forEach((shapeSize) => {
        const selectOptionEditSize = document.createElement('option');
        selectOptionEditSize.setAttribute('value', shapeSize);
        selectOptionEditSize.innerText = shapeSize;
        selectEditSize.appendChild(selectOptionEditSize);
      });
      // creating 'move shape' button
      const buttonMoveShape = document.createElement('button');
      buttonMoveShape.innerText = 'Move';
      divEdit.appendChild(buttonMoveShape);
      // creating 'delete shape' button
      const buttonDeleteShape = document.createElement('button');
      buttonDeleteShape.innerText = 'Delete';
      buttonDeleteShape.className = 'delete-button';
      divEdit.appendChild(buttonDeleteShape);
      buttonDeleteShape.addEventListener('click', () => {
        div.remove();
        this.editing = false;
      });
      // creating moving navigation buttons
      const iArrowUpMoveShape = document.createElement('i');
      iArrowUpMoveShape.className = 'arrow up outline moving icon';
      div.appendChild(iArrowUpMoveShape);
      iArrowUpMoveShape.addEventListener('click', () => {
        const currentShapeTopPosEveL = div.style.top.slice(0, -2);
        div.style.top = `${Number(currentShapeTopPosEveL) - 10}px`;
        if (currentShapeTopPosEveL < 163) {
          divEdit.style.top = '65px';
        } else {
          divEdit.style.top = '-160px';
        }
      });
      const iArrowLeftMoveShape = document.createElement('i');
      iArrowLeftMoveShape.className = 'arrow left outline moving icon';
      if (div.className === 'triangle') {
        iArrowLeftMoveShape.style.left = '-96px';
      }
      div.appendChild(iArrowLeftMoveShape);
      iArrowLeftMoveShape.addEventListener('click', () => {
        const currentShapeLeftPos = div.style.left.slice(0, -2);
        div.style.left = `${currentShapeLeftPos - 10}px`;
      });
      const iArrowRightMoveShape = document.createElement('i');
      iArrowRightMoveShape.className = 'arrow right outline moving icon';
      div.appendChild(iArrowRightMoveShape);
      iArrowRightMoveShape.addEventListener('click', () => {
        const currentShapeLeftPos = div.style.left.slice(0, -2);
        div.style.left = `${Number(currentShapeLeftPos) + 10}px`;
      });
      const iArrowDownMoveShape = document.createElement('i');
      iArrowDownMoveShape.className = 'arrow down outline moving icon';
      div.appendChild(iArrowDownMoveShape);
      iArrowDownMoveShape.addEventListener('click', () => {
        const currentShapeTopPosEveL = div.style.top.slice(0, -2);
        div.style.top = `${Number(currentShapeTopPosEveL) + 10}px`;
        if (currentShapeTopPosEveL < 163) {
          divEdit.style.top = '65px';
        } else {
          divEdit.style.top = '-160px';
        }
      });
      const buttonFinishMovingShape = document.createElement('button');
      buttonFinishMovingShape.className = 'finish-moving-btn moving';
      buttonFinishMovingShape.innerText = 'OK';
      div.appendChild(buttonFinishMovingShape);
      buttonFinishMovingShape.addEventListener('click', () => {
        divEdit.style.display = 'block';
        this.editing = true;
        this.moving = false;
        iArrowUpMoveShape.style.display = 'none';
        iArrowRightMoveShape.style.display = 'none';
        iArrowLeftMoveShape.style.display = 'none';
        iArrowDownMoveShape.style.display = 'none';
        buttonFinishMovingShape.style.display = 'none';
      });
      buttonMoveShape.addEventListener('click', () => {
        this.moving = true;
        this.editing = false;
        this.editingShapeDivIdStr = div.id;
        divEdit.style.display = 'none';
        iArrowUpMoveShape.style.display = 'block';
        iArrowRightMoveShape.style.display = 'block';
        iArrowLeftMoveShape.style.display = 'block';
        iArrowDownMoveShape.style.display = 'block';
        buttonFinishMovingShape.style.display = 'block';
      });
      // listening size changing to change navigation buttons
      selectEditSize.addEventListener('change', () => {
        this.currentShapeSize = Number(selectEditSize.options[selectEditSize.selectedIndex].text);
        if (div.className === 'triangle') {
          div.style.borderBottomWidth = `${40 * this.currentShapeSize}px`;
          div.style.borderRightWidth = `${40 * this.currentShapeSize}px`;
          div.style.borderLeftWidth = `${40 * this.currentShapeSize}px`;
          iArrowLeftMoveShape.style.top = `${(40 * this.currentShapeSize) - 180}px`;
          iArrowLeftMoveShape.style.left = `${(-40 * this.currentShapeSize) - 60}px`;
          iArrowLeftMoveShape.style.top = `${(20 * this.currentShapeSize) - 39}px`;
          iArrowRightMoveShape.style.top = `${(20 * this.currentShapeSize) - 67}px`;
          iArrowRightMoveShape.style.left = `${(40 * this.currentShapeSize) + 25}px`;
          iArrowDownMoveShape.style.top = `${(40 * (this.currentShapeSize)) - 53}px`;
          buttonFinishMovingShape.style.top = `${(20 * (this.currentShapeSize)) - 127}px`;
        } else {
          div.style.width = `${40 * this.currentShapeSize}px`;
          div.style.height = `${40 * this.currentShapeSize}px`;
          iArrowUpMoveShape.style.left = `${(20 * this.currentShapeSize) - 15}px`;
          iArrowLeftMoveShape.style.top = `${(20 * this.currentShapeSize) - 39}px`;
          iArrowRightMoveShape.style.top = `${(20 * this.currentShapeSize) - 67}px`;
          iArrowRightMoveShape.style.left = `${(40 * this.currentShapeSize) + 25}px`;
          iArrowDownMoveShape.style.top = `${(40 * (this.currentShapeSize)) - 53}px`;
          iArrowDownMoveShape.style.left = `${(20 * (this.currentShapeSize)) - 15}px`;
          buttonFinishMovingShape.style.top = `${(20 * (this.currentShapeSize)) - 127}px`;
          buttonFinishMovingShape.style.left = `${(20 * (this.currentShapeSize)) - 14.8}px`;
        }
      });
      // listening to keyboard buttons pressing, to move the shape using keyboard buttons
      window.addEventListener('keydown', (event) => {
        if (this.moving === true) {
          this.editingDivId = document.getElementById(this.editingShapeDivIdStr);
          const currentShapeTopPosEveL = Number(this.editingDivId.style.top.slice(0, -2));
          const currentShapeLeftPosEveL = Number(this.editingDivId.style.left.slice(0, -2));
          if (event.defaultPrevented) {
            return;
          }
          switch (event.key) {
            case 'ArrowDown': {
              this.editingDivId.style.top = `${currentShapeTopPosEveL + 10}px`;
              if (currentShapeTopPosEveL < 163) {
                divEdit.style.top = '65px';
              } else {
                divEdit.style.top = '-160px';
              }
              break;
            }
            case 'ArrowUp': {
              this.editingDivId.style.top = `${currentShapeTopPosEveL - 10}px`;
              if (currentShapeTopPosEveL < 163) {
                divEdit.style.top = '65px';
              } else {
                divEdit.style.top = '-160px';
              }
              break;
            }
            case 'ArrowLeft': {
              this.editingDivId.style.left = `${currentShapeLeftPosEveL - 10}px`;
              break;
            }
            case 'ArrowRight': {
              this.editingDivId.style.left = `${currentShapeLeftPosEveL + 10}px`;
              break;
            }
            case 'Enter': {
              document.querySelectorAll('.moving').forEach((el) => {
                el.style.display = 'none';
              });
              this.moving = false;
              break;
            }
            default: {
              break;
            }
          }
        }
      });
      // creating close button
      const buttonCloseEdit = document.createElement('button');
      buttonCloseEdit.innerText = 'Close';
      buttonCloseEdit.className = 'close-edit-button';
      divEdit.appendChild(buttonCloseEdit);
      buttonCloseEdit.addEventListener('click', () => {
        divEdit.style.display = 'none';
        this.editing = false;
      });
      // listening a double-click to show edit panel
      div.addEventListener('dblclick', () => {
        if (!this.editing && !this.moving) {
          divEdit.style.display = 'block';
          this.editing = true;
        }
      }, false);
      // changing ID counter at the end
      this.shapeID = this.shapeID + 1;
    },
  },
  data() {
    return {
      shapeID: 1,
      currentShape: null,
      shapeBackgroundColor: '#444444',
      mouseX: null,
      mouseY: null,
      editing: null,
      dragging: null,
      moving: null,
      movingShapeDivIdStr: null,
      shapeSizes: [1, 2, 3, 4, 5],
      currentShapeSize: 1,
      editingDivId: null,
      eventsListenersOn: null,
    };
  },
};
</script>


<style>
*{
  font-family: 'Verdana', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  user-select: none;
}
.title-contr {
  position: fixed;
  text-align: center;
  background-color: white;
  z-index: 7;
}
.container{
  position: fixed;
  top: -10px;
  left: 0px;
  min-height: 80vh;
  width: 100%;
  text-align: center;
  margin: 10px auto;
  border: 3px solid #c3c3c3;
  background: conic-gradient(from 90deg at 2px 2px,#f5f4f4 90deg,#e0e0e0 0) -2px -2px/30px 30px;
}
.moving-hint-h2{
  margin-top: 40px!important;
  animation: smooth 15s ease-out;
  color: #707070;
  opacity: 0.2;
}
@keyframes smooth {
    0% { opacity: 1;}
    100% { opacity: 0.2;}
}
.square {
  height: 40px;
  width: 40px;
  background-color: #444444;
  display: inline-block;
  z-index: 5;
}
.triangle {
  width: 0;
  height: 0;
  border-left: 40px solid transparent;
  border-right: 40px solid transparent;
  border-bottom: 40px solid #444444;
  z-index: 5;
}
.circle {
  height: 40px;
  width: 40px;
  background-color: #444444;
  border-radius: 50%;
  display: inline-block;
  z-index: 5;
}
.dragging {
  position: absolute;
  pointer-events:none;
  background-color:#ff4f49;
  z-index: 10;
}
.dragging.triangle {
  border-bottom-color:#ff4f49;
  background-color: transparent;
}
.edit-panel {
  position: relative;
  top: -160px;
  left: -55px;
  width: 160px;
  height: 150px;
  padding:3px;
  background-color: white;
  z-index: 6;
  display: none;
  border: 1px solid #c0c0c0;
}
.triangle .edit-panel {
  left: -80px;
}
.edit-panel * {
  margin:5px 4px;
}
.edit-panel #sizeEditor {
  padding: 2px 2px 2px 15px;
  margin: 2px 0px 2px 11px;
}
.edit-panel button {
  margin-left: 6px;
  padding: 2px 10.5px;
}
.edit-panel .delete-button {
  color:#c42626;
}
.edit-panel .close-edit-button {
  padding: 2px 50px;
}
i.up,
i.left,
i.right,
i.down {
  position: relative;
  font-size: 2em;
  display: none;
  color: #747474;
}
i.up {
  top: -51px;
  left: 5px;
}
i.left{
  top: -19px;
  left: -57px;
}
i.right{
  top: -47px;
  left: 65px;
}
i.down{
  top: -8px;
  left: 5px;
}
.triangle i.up,
.triangle i.down{
  left: -17px!important;
}
.finish-moving-btn {
  position: relative;
  top: -107px;
  left: 5.2px;
  padding: 1px;
  width: 30px;
  height: 30px;
  font-size: 11pt;
  font-weight: bold;
  display: none;
  color: #383838;
}
.triangle .finish-moving-btn {
  left: -15.5px;
}
.footer {
  position: fixed;
  left: 0;
  bottom: 0;
  padding: 20px;
  width: 100%;
  height: 20vh;
  border: 3px solid #c3c3c3;
  color: #707070;
  background-color: white;
  text-align: center;
  z-index: 9;
}
.footer div {
  display: inline-block;
  margin: auto 10px;
}
</style>

