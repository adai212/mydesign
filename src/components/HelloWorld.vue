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
            <b-col sm="8">
              <b-form-input :id="`name`" :type="`text`" v-model="newItem.name"></b-form-input>
            </b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label :for="`width`">width:</label>
            </b-col>
            <b-col sm="8">
              <b-form-input :id="`width`" :type="`text`" v-model="newItem.width"></b-form-input>
            </b-col>
            <b-col sm="1">mm</b-col>
          </b-row>
          <b-row class="my-1">
            <b-col sm="3">
              <label :for="`height`">height:</label>
            </b-col>
            <b-col sm="8">
              <b-form-input :id="`height`" :type="`text`" v-model="newItem.height"></b-form-input>
            </b-col>
            <b-col sm="1">mm</b-col>
          </b-row>
        </b-container>
      </b-modal>
    </div>
    <div>
      <div v-for="item in items" :key="item.num">
        <div
          class="design-item"
          :title="item.width + '*' + item.height"
          v-bind:style="{ width: _.round(item.width * scale) + 'px', height: _.round(item.height * scale) + 'px', left: item.left + 'px', top: item.top + 'px' }"
          v-draggable="draggableValue"
          @contextmenu.prevent.stop="handleClick($event, item)"
        >
          {{item.name}}
          <br />
          {{item.width + '*' + item.height}}
        </div>
      </div>
    </div>
    <vue-simple-context-menu
      :elementId="'myUniqueId'"
      :options="options"
      :ref="'vueSimpleContextMenu'"
      @option-clicked="optionClicked"
    />
    <!-- <div class="design-data">
      <div v-for="item in items" :key="item.num">{{item}}</div>
    </div>-->
    <div class="design-product">
      <b-table striped hover :items="displayed" :fields="fields">
        <template v-slot:cell()="data">
          <b-form-input
            type="text"
            v-model="data.value"
            :value="data.value"
            @input="changeDisplay(data)"
          ></b-form-input>
        </template>
        <template v-slot:cell(operate)="data">
          <b-button variant="danger" @click="deleteDisplay(data)">delete</b-button>
        </template>
      </b-table>
      <b-alert show>合计: {{displayedSum}}</b-alert>
      <b-button variant="primary" @click="addDisplay()">add</b-button>
      <b-button variant="primary" @click="exportDisplay()">export</b-button>
    </div>
  </div>
</template>

<script>
import { Draggable } from "draggable-vue-directive";
import $ from "jquery";

var appData = require("../data.json");
var displayedData = require("../displayed.json")

export default {
  name: "myDesign",
  data() {
    return {
      editFlag: false,
      editIndex: 0,
      scale: 0.0706,
      newItem: {
        name: "",
        width: "",
        height: "",
        top: "",
        left: ""
      },
      items: appData,
      draggableValue: {
        onPositionChange: this.onPositionChange
      },
      options: [
        { name: "edit", slug: "edit" },
        { name: "delete", slug: "delete" }
      ],
      fields: ["product", "price", "operate"],
      displayed: displayedData
    };
  },
  computed: {
    displayedSum: function() {
      let sum = 0;
      for (let i in this.displayed) {
        let item = this.displayed[i];
        sum += parseFloat(item.price || 0);
      }
      return sum;
    }
  },
  methods: {
    createItem() {
      this.editFlag = false;
      this.newItem = {
        name: "",
        width: "",
        height: "",
        top: "100",
        left: "100"
      };
    },
    confirmModal() {
      if (this.editFlag) {
        let editItem = this.items[this.editIndex];
        for (let key in editItem) {
          editItem[key] = this.newItem[key];
        }
      } else {
        let newItem = this._.clone(this.newItem);
        this.items.push(newItem);
      }
    },
    exportItems() {
      console.log(JSON.stringify(this.items));
    },
    onPositionChange: function(positionDiff, absolutePosition, event) {
      if (absolutePosition) {
        this.items[
          $(event.target)
            .parent()
            .index()
        ].top = absolutePosition.top;
        this.items[
          $(event.target)
            .parent()
            .index()
        ].left = absolutePosition.left;
      }
    },
    handleClick(event, item) {
      this.editIndex = $(event.target)
        .parent()
        .index();
      this.$refs.vueSimpleContextMenu.showMenu(event, item);
    },
    optionClicked(event) {
      switch (event.option.name) {
        case "edit":
          this.editFlag = true;
          this.newItem = this._.clone(this.items[this.editIndex]);
          this.$bvModal.show("new-item");
          break;
        case "delete":
          this.items.splice(this.editIndex, 1);
          break;
      }
    },
    deleteDisplay(data) {
      this.displayed.splice(data.index, 1);
    },
    changeDisplay(data) {
      this.displayed[data.index][data.field.key] = data.value;
    },
    addDisplay() {
      let item = {
        product: "",
        price: "",
        operate: ""
      };
      this.displayed.push(item)
    },
    exportDisplay() {
      console.log(JSON.stringify(this.displayed));
    }
  },
  directives: {
    Draggable
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.design-bg {
  background-image: url("../assets/mydesign.png");
  background-repeat: no-repeat;
  width: 900px;
  height: 900px;
  transform: scale(1);
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
  font-size: 12px;
}
.design-data {
  position: absolute;
  right: 0;
  top: 0;
  width: 400px;
  font-size: 14px;
}
.design-product {
  position: absolute;
  width: 400px;
  right: 15px;
  top: 0;
}
</style>
