<template>
  <div class="complete">
    {{ title }}
    <div class="input">
      <draggable
        class="dragArea list-group"
        :list="values"
        :group="{ name: 'people', pull: true, put: true }"
        :move="checkMove"
      >
        <div v-for="value in values" :key="value.id">
          <v-chip
            class="ma-1"
            close
            @click:close="remove(value.id)"
            :key="value.id"
          >
            {{ value.label }}
          </v-chip>
        </div>
      </draggable>
      <draggable
        class="dragArea list-group"
        :list="values"
        :group="{ name: 'people', pull: false, put: true }"
      >
        <div class="input-Box ma-1">
          <input
            type="text"
            class="input-ids"
            v-model="value"
            @keyup.down="currentDropdownSelection++"
            @keyup.up="selectNext"
            @input="currentDropdownSelection = 0"
            @keyup.enter="enterValueFromAutocomplete"
          />
        </div>
      </draggable>
    </div>
    <div class="autocomplete" v-if="autocomplete.length > 0">
      <div
        v-for="(item, index) in autocomplete"
        :key="index"
        :class="{ active: index == currentDropdownSelection, result: true }"
        @click="addFromAutocomplete(item)"
      >
        {{ item.label }}
        <span v-for="(ip, index2) in item.ips" :key="index2">{{ ip }} / </span>
      </div>
    </div>
    <pre>{{ formattedJson }}</pre>
  </div>
</template>

<script>
import ips from "../constants/ips";
import draggable from "vuedraggable";

export default {
  name: "Autocomplete",
  data() {
    return {
      value: "",
      values: [],
      ips,
      currentDropdownSelection: 0
    };
  },
  filters: {
    pretty: function(value) {
      return JSON.stringify(JSON.parse(value), null, 2);
    }
  },
  props: {
    title: String
  },
  components: {
    draggable
  },
  methods: {
    addFromAutocomplete(item) {
      if (this.checkIfAlreadyExist(this.values, item.id))
        this.values.push({
          id: item.id,
          label: item.label,
          ips: item.ips
        });
      this.value = "";
    },
    remove(id) {
      this.values = this.values.filter(item => {
        return item.id !== id;
      });
    },
    checkIfAlreadyExist(arrayofValues, id) {
      let alreadyExist = true;
      arrayofValues.map(item => {
        if (item.id === id) alreadyExist = false;
      });
      return alreadyExist;
    },
    checkMove: function(evt) {
      return this.checkIfAlreadyExist(
        evt.relatedContext.list,
        evt.draggedContext.element.id
      );
    },
    selectNext() {
      console.log("select");
      this.currentDropdownSelection--;
    },
    enterValueFromAutocomplete() {
      if (this.autocomplete.length !== 0)
        this.addFromAutocomplete(
          this.autocomplete[this.currentDropdownSelection]
        );
    }
  },
  computed: {
    autocomplete() {
      if (this.value == "") return [];
      return this.ips.filter(
        ip => ip.label.toLowerCase().indexOf(this.value.toLowerCase()) >= 0
      );
    },
    formattedJson() {
      return JSON.stringify(this.values, null, 2);
    }
  }
};
</script>
<style lang="scss">
.complete {
  margin: 50px;
  width: 50%;
}
.dragArea.list-group {
  // flex: 0;
  display: flex;
  flex-wrap: wrap;
}
.input {
  border: 2px solid #000;
  display: flex;
  flex-wrap: wrap;
  max-width: 100%;
  .list-group:last-child {
    // flex-basis: 100%;
    // flex-shrink: 0;
    // flex-grow: 1;
    flex: 1;
  }
}

input {
  width: 100%;
}
input:focus,
textarea {
  outline: none !important;
}
.input-Box {
  // border: 1px solid black;
  width: 100%;
}
.input-ids {
  height: 100%;
}
.autocomplete {
  width: 100%;
  border: 1px solid black;
  font-size: 14px;
  .result {
    width: 100%;
    border-bottom: 1px solid gray;
    &:hover,
    &.active {
      background: #f4f3f3;
      cursor: pointer;
    }
    span {
      color: grey;
      font-size: 10px;
    }
  }
}
</style>
