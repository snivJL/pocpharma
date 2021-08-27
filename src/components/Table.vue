<template>
  <h2>Found {{ fengShuiList.length }} fengshui numbers!</h2>
  <table v-if="fengShuiList">
    <thead>
      <th>Logo</th>
      <th>Operator</th>
      <th>Number</th>
    </thead>
    <tbody>
      <tr v-for="(item, index) in fengShuiList" :key="index">
        <td>
          <img :src="getImgUrl(item.operator)" v-bind:alt="item.operator" />
        </td>
        <td>{{ item.operator }}</td>
        <td>{{ item.num }}</td>
      </tr>
    </tbody>
  </table>
</template>
<script>
export default {
  props: {
    fengShuiList: [],
  },
  methods: {
    getImgUrl(operator) {
      return require("../assets/media/" + operator + ".svg");
    },
  },
};
</script>
<style scoped>
table {
  background: white;
  width: 100%;
  border-collapse: collapse;
}
tr,
th,
td {
  border: 1px solid #009578;
  padding: 3%;
  text-align: center;
  text-transform: capitalize;
}
img {
  max-width: 50px;
}
</style>
