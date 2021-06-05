<template>
  <div class="row">
    <div class="col" style="">
      <div class="btn-group " :class="[{ [`drop${direction}`]: direction }]">
        <button
          class="btn dropdown-toggle "
          type="button"
          data-toggle="dropdown"
          aria-haspopup="true"
          :class="[
            { show: isOpen },
            { [`btn${outline ? `-outline` : ``}-${type}`]: type },
            { [`btn-${btnsize}`]: btnsize },
          ]"
          :aria-expanded="isOpen"
          @click="ClickEvent"
          v-click-outside="CloseItem"
        >
          {{ text }}
        </button>
        <ul
          class="dropdown-menu shadow p-3 bg-white rounded "
          :class="[{ show: isOpen }]"
          v-on:click.stop
        >
          <nav aria-label="Page navigation example">
            <div class="pagination d-flex justify-content-between">
              <li class="page-item" :class="[{ disabled: backDisabled }]">
                <a class="page-link" type="button" @click="back">
                  <span aria-hidden="true">&laquo;</span></a
                >
              </li>
              <li class="page-item">
                <div class="form-group" style="padding:0 0.35em 0 0.35em">
                  <input
                    type="text"
                    class="form-control input-sm"
                    v-model="keyword"
                    v-on:keyup="search"
                    placeholder="Search"
                  />
                </div>
              </li>
              <li class="page-item" :class="[{ disabled: nextDisabled }]">
                <a class="page-link" type="button" @click="next"
                  ><span aria-hidden="true">&raquo;</span></a
                >
              </li>
            </div>
          </nav>
          <table class="table  table-borderless table-icon">
            <tbody id="tbBody" @click="selectIcon"></tbody>
          </table>
          <div class="dropdown-divider"></div>
          <div class="d-flex justify-content-center">
            <span class="pagination-info">
              Showing {{ index * size - size }} to
              {{
                filtered.length > index * size ? index * size : filtered.length
              }}
              of {{ filtered.length }}
            </span>
          </div>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import { ListIcon } from "./icon.js";
export default {
  name: "v-icon-picker",
  props: {
    text: {
      type: String,
      default: "Icon",
    },
    direction: {
      type: String,
      default: "down",
      validator(x) {
        return ["down", "up", "left", "right"].indexOf(x) !== -1;
      },
    }, 
    type: {
      type: String,
      default: "dark",
      validator(x) {
        return (
          [
            "primary",
            "success",
            "secondary",
            "danger",
            "warning",
            "info",
            "light",
            "dark",
            "default",
          ].indexOf(x) !== -1
        );
      },
    }, 
    rowsize: { type: Number, default: 4 },
    colsize: { type: Number, default: 4 }, 
    btnsize: {
      type: String,
      validator(x) {
        return ["lg", "sm"].indexOf(x) !== -1;
      },
    },
    outline: {
      type: Boolean,
      default: false,
    },
  },
  model: {
    event: "change",
  },
  data() {
    return {
      icon: ListIcon, 
      index: 0, 
      maxIndex: 0,
      nextDisabled: true, 
      backDisabled: true, 
      filtered: [], 
      available: [], 
      isOpen: false,
      keyword: "",
      size: this.rowsize * this.colsize,
    };
  },
  mounted: function() {
    this.filtered = this.icon;
  },
  methods: {
    isLike: function(value) {
      return value.includes(this.keyword);
    },
    search: async function() {
      if (this.keyword != null && this.keyword != "")
        this.filtered = this.icon.filter(this.isLike);
      else this.filtered = this.icon;
    },

    next: function() {
      this.index += this.maxIndex > this.index ? 1 : 0;
    },
    back: function() {
      this.index -= this.index > 1 ? 1 : 0;
    },
    ClickEvent: function() {
      this.isOpen = !this.isOpen;
    },
    CloseItem() {
      this.isOpen = false;
    },
    randomID: function(item) {
      return `ID${item}${parseInt(Math.random() * 99).toString()}`;
    },
    selectIcon: function(e) {
      let iconSelected = "";
      switch (e.target.tagName.toLowerCase()) {
        case "button":
          iconSelected = e.target.getAttribute("data");
          break;
        case "i":
          iconSelected = e.target.getAttribute("class");
          break;
      }
      if (iconSelected == "") return;
      this.$emit("change", iconSelected);
      this.keyword = iconSelected;
    },
    initTable: async function() {
      let length = this.available.length;
      let tb = document.getElementById("tbBody");
      tb.innerHTML = "";
      let tr = "";
      let j = 0;
      let i = 0;
      while (i < this.colsize) {
        if (i == 0) tr = "<tr>";
        if (j < length) {
          tr += `<td>
                    <button type="button" data="${this.available[j]}" class="btn btn-outline-${this.type}">
                        <i class="${this.available[j]}"></i>
                      </button>
                </td>`;
        } else {
          tr += "</tr>";
          tb.innerHTML += tr;
          break;
        }
        if (i == this.colsize - 1) {
          tr += "</tr>";
          tb.innerHTML += tr;
          i = 0;
        } else i++;
        j++;
      }
    },
  },
  watch: {
    keyword: {
      handler: function() {
      },
      deep: true,
      immediate: false,
    },
    index: {
      handler: function() {
        let endLength = this.index * this.size;
        this.available = this.filtered.slice(endLength - this.size, endLength);
        this.nextDisabled = this.index < this.maxIndex ? false : true;
        this.backDisabled = this.index > 1 ? false : true;
      },
      deep: true,
      immediate: false,
    },
    maxIndex: {
      handler: function() {
        this.nextDisabled = this.index < this.maxIndex ? false : true;
      },
      deep: true,
      immediate: false,
    },

    filtered: {
      handler: function() {
        let length = this.filtered.length;
        if (length > 0) {
          this.available =
            length >= this.size
              ? this.filtered.slice(0, this.size)
              : this.filtered;
        } else this.available = [];
        if (length <= this.size) this.maxIndex = 1;
        else if (this.filtered.length % this.size == 0)
          this.maxIndex = this.filtered.length / this.size;
        else
          this.maxIndex =
            Math.floor(parseInt(this.filtered.length) / this.size) + 1;
        this.index = 1;
      },
      deep: true,
      immediate: false,
    },
    available: {
      handler: function() {
        this.initTable();
      },
      deep: true,
      immediate: false,
    },
  },
};
</script>
<style>
.table.table-icon th,
.table.table-icon td {
  padding: 0.25rem !important;
}
.has-search .form-control-feedback {
  right: initial;
  left: 0;
  color: #ccc;
}
.has-search .form-control {
  padding-right: 12px;
  padding-left: 34px;
}
.disabled {
  pointer-events: none;
}
.pagination-info {
  font-size: 0.75em;
  font-weight: 600;
  color: #4c5761;
}
.fade {
  opacity: 0;
  -webkit-transition: opacity 0.15s linear;
  -o-transition: opacity 0.15s linear;
  transition: opacity 0.15s linear;
}
</style>
