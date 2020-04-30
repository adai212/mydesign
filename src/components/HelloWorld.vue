<template>
  <div>
    <div class="design-bg"></div>
    <div>
      <div class="design-btn">
        <b-button v-b-modal.new-item variant="primary" @click="createItem()">new</b-button>
        <b-button variant="primary" @click="exportItems()">export</b-button>
      </div>
      <b-modal id="new-item" title="new item" ref="modal" @ok="confirmModal">
        <b-container fluid>
          <b-row class="my-1">
            <b-col sm="3">
              <label :for="`name`">name:</label>
            </b-col>
            <b-col sm="9">
              <b-form-input :id="`name`" :type="`text`" v-model="newItem.name"></b-form-input>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label :for="`width`">width:</label>
            </b-col>
            <b-col sm="9">
              <b-form-input :id="`width`" :type="`text`" v-model="newItem.width"></b-form-input>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label :for="`height`">height:</label>
            </b-col>
            <b-col sm="9">
              <b-form-input :id="`height`" :type="`text`" v-model="newItem.height"></b-form-input>
            </b-col>
          </b-row>
        </b-container>
      </b-modal>
    </div>
    <div v-for="item in items" :key="item.num">
      <div
        class="design-item"
        v-bind:style="{ width: item.width + 'px', height: item.height + 'px', left: item.left + 'px', top: item.top + 'px', lineHeight: item.height + 'px' }"
        v-draggable
      >{{item.name}}</div>
    </div>
  </div>
</template>

<script>
import { Draggable } from "draggable-vue-directive";

export default {
  name: "myDesign",
  data() {
    return {
      newItem: {
        name: "",
        width: "",
        height: "",
        top: "",
        left: ""
      },
      items: [
        {
          name: "test",
          width: "100",
          height: "100",
          top: "100",
          left: "100"
        }
      ]
    };
  },
  methods: {
    createItem() {
      this.newItem = {
        name: "",
        width: "",
        height: "",
        top: "100",
        left: "100"
      };
    },
    confirmModal() {
      let newItem = this._.clone(this.newItem);
      this.items.push(newItem);
    },
    exportItems() {
      console.log(JSON.stringify(this.items));
    }
  },
  directives: {
    Draggable
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.design-bg{
  background-image: url('../assets/logo.png');
  background-repeat: no-repeat;
  width: 200px;
  height: 200px;
  transform: scale(2);
  transform-origin: 0 0;
}
.design-btn {
  position: absolute;
  left: 30px;
  top: 30px;
}
.design-item {
  display: inline-block;
  position: absolute;
  background-color: #f0f0f0;
}
</style>
