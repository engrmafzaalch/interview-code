<template>
  <div>
    <div class="filters">
      <label for="">Where</label>
      <select name="" id="" @change="colSelected">
        <option value="" selected default>Select</option>
        <option :value="h" v-for="h in headings" :key="h">{{ h }}</option>
      </select>
      <select name="" id="" @change="secondSelected" v-show="firstSelected">
        <option value="" selected default>Select</option>

        <option :value="h" v-for="h in secondFilter" :key="h">{{ h }}</option>
      </select>
      <input
        :type="inputType"
        v-show="secondFilterSelected"
        v-model="filterVal"
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
      firstSelected: false,
      secondFilterSelected: false,
      firstVal: "",
      secondFilter: [],
      secondVal: "",
      filterVal: "",
      inputType: "",
    };
  },
  methods: {
    colSelected(ev) {
      this.firstSelected = true;
      this.filterVal = "";
      this.secondFilterSelected = false;
      const selected = ev.target.value;
      this.firstVal = selected;
      const type = typeof this.borrowers[0][selected];
      console.log(type);
      if (selected === "dateOfBirth" || selected === "startDate") {
        // this.firstVal = "date";
        this.secondFilter = ["greaterThan", "lessThan"];
        this.inputType = "date";
      } else if (type === "string") {
        this.secondFilter = ["equals", "includes"];
        this.inputType = "text";
      } else if (type === "number") {
        this.secondFilter = ["greaterThan", "lessThan"];
        this.inputType = "number";
      }
    },
    secondSelected(ev) {
      this.secondVal = ev.target.value;
      this.secondFilterSelected = true;
    },
  },
  computed: {
    headings() {
      return Object.keys(this.borrowers[0]);
    },
    rows() {
      if (!this.filterVal) return this.borrowers;
      else if (this.secondVal == "includes")
        return this.borrowers.filter((b) =>
          b[this.firstVal].toLowerCase().includes(this.filterVal.toLowerCase())
        );
      else if (this.secondVal == "equals")
        return this.borrowers.filter(
          (b) => b[this.firstVal].toLowerCase() === this.filterVal.toLowerCase()
        );
      else if (this.inputType === "number" && this.secondVal == "greaterThan")
        return this.borrowers.filter((b) => b[this.firstVal] > +this.filterVal);
      else if (this.inputType === "number" && this.secondVal == "lessThan")
        return this.borrowers.filter((b) => b[this.firstVal] < +this.filterVal);
      else if (this.inputType === "date" && this.secondVal == "greaterThan")
        return this.borrowers.filter((b) => {
          return (
            new Date(b[this.firstVal]).getTime() >
            new Date(this.filterVal).getTime()
          );
        });
      else if (this.inputType === "date" && this.secondVal == "lessThan")
        return this.borrowers.filter((b) => {
          return (
            new Date(b[this.firstVal]).getTime() <
            new Date(this.filterVal).getTime()
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
