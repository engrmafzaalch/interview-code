<template>
  <div>
    <div class="filters">
      <label for="">Where</label>
      <select name="" id="" @change="colSelected">
        <option value="" selected default>Select</option>
        <option :value="h" v-for="h in headings" :key="h">{{ h }}</option>
      </select>
      <select name="" id="" @change="secondSelected" v-show="primaryFilterSelected">
        <option value="" selected default>Select</option>

        <option :value="h" v-for="h in secondFilterDropdown" :key="h">{{ h }}</option>
      </select>
      <input
        :type="inputType"
        v-show="secondFilterSelected"
        v-model="thirdFilterVal"
      />
    </div>
    <table>
      <thead>
        <tr>
          <td v-for="h in headings" :key="h">
            {{ h }}
          </td>
        </tr>
      </thead>
      <tbody>
        <tr v-for="row in rows" :key="row.id">
          <td v-for="cell in row" :key="cell">{{ cell }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import borrowersData from "@/data/borrowers.json";
export default {
  name: "IndexPage",
  data() {
    return {
      borrowers: borrowersData,
      primaryFilterSelected: false,
      secondFilterSelected: false,
      primaryFilterVal: "",
      secondFilterDropdown: [],
      secondFilterVal: "",
      thirdFilterVal: "",
      inputType: "",
    };
  },
  methods: {
    colSelected(ev) {
      this.primaryFilterSelected = true;
      this.thirdFilterVal = "";
      this.secondFilterSelected = false;
      const selected = ev.target.value;
      this.primaryFilterVal = selected;
      const type = typeof this.borrowers[0][selected];
      
      if (selected === "dateOfBirth" || selected === "startDate") {
        this.secondFilterDropdown = ["greaterThan", "lessThan"];
        this.inputType = "date";
      } else if (type === "string") {
        this.secondFilterDropdown = ["equals", "includes"];
        this.inputType = "text";
      } else if (type === "number") {
        this.secondFilterDropdown = ["greaterThan", "lessThan"];
        this.inputType = "number";
      }
    },
    secondSelected(ev) {
      this.secondFilterVal = ev.target.value;
      this.secondFilterSelected = true;
    },
  },
  computed: {
    headings() {
      return Object.keys(this.borrowers[0]);
    },
    rows() {
      if (!this.thirdFilterVal) return this.borrowers;
      else if (this.secondFilterVal == "includes")
        return this.borrowers.filter((b) =>
          b[this.primaryFilterVal].toLowerCase().includes(this.thirdFilterVal.toLowerCase())
        );
      else if (this.secondFilterVal == "equals")
        return this.borrowers.filter(
          (b) => b[this.primaryFilterVal].toLowerCase() === this.thirdFilterVal.toLowerCase()
        );
      else if (this.inputType === "number" && this.secondFilterVal == "greaterThan")
        return this.borrowers.filter((b) => b[this.primaryFilterVal] > +this.thirdFilterVal);
      else if (this.inputType === "number" && this.secondFilterVal == "lessThan")
        return this.borrowers.filter((b) => b[this.primaryFilterVal] < +this.thirdFilterVal);
      else if (this.inputType === "date" && this.secondFilterVal == "greaterThan")
        return this.borrowers.filter((b) => {
          return (
            new Date(b[this.primaryFilterVal]).getTime() >
            new Date(this.thirdFilterVal).getTime()
          );
        });
      else if (this.inputType === "date" && this.secondFilterVal == "lessThan")
        return this.borrowers.filter((b) => {
          return (
            new Date(b[this.primaryFilterVal]).getTime() <
            new Date(this.thirdFilterVal).getTime()
          );
        });
    },
  },
};
</script>
<style scoped>
table {
  border-collapse: collapse;
}
thead {
  background: #eee;
}
thead td {
  padding: 10px 4px;
  text-align: center;

  border: 1px solid #333;
}
tbody tr:nth-child(even) {
  background: #eee;
}

tbody td {
  padding: 10px 4px;
  border-right: 1px solid #333;
  border-left: 1px solid #333;
  text-align: center;
  font-size: 12px;
}
input,
select {
  height: 42px;
  background: #eee;
  font-size: 18px;
}
</style>
