<template>
  <tr :class="['animate__animated','created',{'deleted':isDel}]" >
    <td class="text-center">
      <input type="hidden" :value="record.id">
      <span class="show-in-print">{{ index+1 }}</span>
<!--      $emit('toDel',record.id)-->
      <i  @click="[isDel = true,$emit('toDel',record.id)]" class="fa-solid fa-trash-alt text-danger hide-in-print del-btn"></i>
    </td>
    <td>{{ record.service.name }}</td>
    <td class="text-end quantity-width" @dblclick="quantityEdit=true">
      <input v-if="quantityEdit" v-model="record.quantity"  @keyup.enter="exitQuantityEdit" @change="updateQuantity" type="number" class="form-control w-100">
      <span v-else>{{ record.quantity }}</span>
    </td>
    <td class="text-end unitPrice-width">{{ record.service.price }}</td>
    <td class="text-end">{{ record.cost }}</td>
  </tr>
</template>

<script>
export default {
  name: "Row",
  props: {
    record: {
      type: Object,
      // default: 0
    },
    index:{
      type:Number,
    },

  },
  data() {
    return {
      quantityEdit: false,
      isDel:false,
      deleted:'deleted'
    }
  },
  methods: {
    exitQuantityEdit(){
      this.quantityEdit = false;
    },
    updateQuantity() {
      this.record.cost = this.record.service.price * this.record.quantity ;
      console.log(this.record);
    }
  },
}
</script>

<style scoped>
  .quantity-width{
    width: 150px;
  }
  unitPrice-width{
    width: 100px;
  }
  .text-danger{
    color: hotpink !important;
  }
  .del-btn{
    cursor: pointer;
    transition: .4s;
  }
  .del-btn:hover{
    text-shadow: 1px 1px 5px #ff002c;
    transform: rotate(10deg);
  }
  .created{
    animation: .5s fadeInDown;
  }
  .deleted{
    animation: .5s slideOutRight;
  }

</style>