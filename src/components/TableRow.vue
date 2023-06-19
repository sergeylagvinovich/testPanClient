<template>
  <tr>
    <th scope="row">{{index+1}}</th>
    <td class="font-weight-bold">{{item.pan}}</td>
    <td>
      <b-icon-check-lg variant="success" v-if="item.inLocalDB"/>
      <b-icon-x-lg variant="danger" v-else/>
    </td>
    <td>
      <b-icon-check-lg variant="success" v-if="item.inAnotherDB"/>
      <b-icon-x-lg variant="danger" v-else/>
    </td>
  </tr>
</template>

<script>
export default {
  name: "TableRow",
  props:{
    item:{
      type:Object,
    },
    index:{
      type:Number
    }
  }
}
</script>

<style>

</style>
