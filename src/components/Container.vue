<template>
  <div @mousedown.prevent="onMouseDown"  class="Container">
    <ul>
      <Core :id="Date.now().toString()"/>
    </ul>
  </div>
</template>

<script>
import { Draggable } from "@dflex/draggable"
import Core from './Core.vue'

export default {
  name: 'Container',

  components: { Core },

  data: function () {
    return { 
      id: 'parent-1',
      mouseEvents: null,
      draggable: null,
      draggedID: null 
    }
  },

  methods: {
    onMouseDown: function(e)  {
      const { target, button, clientX, clientY } = e;
      // avoid right mouse click and ensure id
      if (typeof button === "number" && button === 0) {
        const { id } = target;
        if (id) {
          this.draggedID = id;
          this.draggable = new Draggable(id, { x: clientX, y: clientY });
          this.mouseEvents = [
            { evType: "mousemove", evTarget: document, handler: this.onMouseMove },
            { evType: "mouseup", evTarget: document, handler: this.onMouseUp },
          ];
          this.mouseEvents.forEach(({ evType, evTarget, handler }) => {
            evTarget.addEventListener(evType, handler);
          });
        }
      }
    },

    onMouseUp: function() {
      if (this.draggedID) {
        this.mouseEvents.forEach(({ evType, evTarget, handler }) => {
          evTarget.removeEventListener(evType, handler);
        });
        this.draggable.endDragging();
      }
    },

    onMouseMove: function(e) {
      if(this.draggedID) {
        const { clientX, clientY } = e;
        this.draggable.dragAt(clientX, clientY);
      }
    }
  }
}
</script>

<style scoped>
.Container {
  display: flex;
  width: 41.53rem;
  /* border: 4px green solid; */
  background: #07718f;
}
ul {
  /* display: flex; */
  flex-direction: column;
  list-style: none;
  margin: 10px;
  padding: 8px;
  border: 4px #bac5c7 solid;
  background: #cecece;
  height: fit-content;
}
</style>
