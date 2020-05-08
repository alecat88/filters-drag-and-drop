<template>
  <div class="complete">
    <div class="input">
      <div v-for="value in values" :key="value.index">
        <v-chip
          draggable
          class="ma-1"
          close
          @click:close="remove(value.id)"
        >
          {{ value.label }}
        </v-chip>
      </div>
      <div class="input-Box ma-1">
        <input
          type="text"
          name="ips"
          class="input-ids"
          v-model="value"
        />
      </div>
    </div>
    <div class="autocomplete" v-if="autocomplete.length > 0">
      <div
        v-for="(item, index) in autocomplete"
        :key="index"
        class="result"
        @click="addFromAutocomplete(index)"
      >
        {{ item.label }}
        <span v-for="(ip, index2) in item.ips" :key="index2">{{ip}} / </span>
      </div>
    </div>
    {{ values }}
  </div>
</template>

<script>
import ips from "../constants/ips";
export default {
  name: "Autocomplete",
  data() {
    return {
      value: "",
      values: [],
      ips
    };
  },
  methods: {
    addFromAutocomplete(index) {
      let item = this.autocomplete[index];
      //if element already exist
      this.values.filter(value => value.id === index)

      //add
      this.values.push({
        id: item.id,
        label: item.label,
        ips: item.ips
      });
      this.value = "";
    },
    remove(id) {
      this.values = this.values.filter(item => {return  item.id !== id})
    }
  },
  computed: {
    autocomplete() {
      if (this.value == "") return [];
      return this.ips.filter(ip => ip.label.toLowerCase().indexOf(this.value.toLowerCase()) >= 0
      );
    }
  }
};
</script>
<style lang="scss">
.complete {
  margin: 50px;
}
.input {
  border: 2px solid #000;
  display: flex;
  flex-wrap: wrap;
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
    &:hover {
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
