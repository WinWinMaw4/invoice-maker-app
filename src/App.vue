<template>
  <div class="container">
    <div class="row">
      <div class="col-12 position-relative">
        <h3 class="text-center my-3 text-primary">
          Invoice Maker
          <a href="" target="_blank" class="hide-in-print">
            <i class="fa fa-file-circle-plus"></i>
          </a>
        </h3>

        <div class="row align-items-center text-black-50 fw-light my-2 my-md-4">
          <div class="col-12 col-md-4 mb-2">
            <span class="d-inline-block">Invoice No : </span>
            <span class="ms-2">{{invoiceId}}</span>
          </div>
          <div class="col-12 col-md-4 mb-2">
            <span class="d-inline-block">Date : </span>
            <span class="ms-2">{{ dayAndDate }}</span>
          </div>
          <div class="col-12 col-md-4 mb-2">
            <span class="d-inline-block">Cashier : </span>
            <span class="ms-2">{{cashier}}</span>
          </div>
        </div>

        <form class="hide-in-print" action="" @submit.prevent="saveRecord">
          <div class="row g-2">
            <div class="col-6">
              <select v-model="selectedService" class="form-select">
                <option value="" >Select Service</option>
                <option v-for="service in services" :value="service.id" :key="service.id">
                  {{ service.name }}
                </option>
              </select>
            </div>
            <div class="col-4">
              <input type="number" v-model="inputQuantity" class="form-control" placeholder="Quantity" min="1">
            </div>
            <div class="col-2">
              <button class="btn btn-primary w-100">
                <i class="fa-solid fa-plus"></i>
              </button>
            </div>
          </div>
        </form>

        <div class="row">
          <div class="col-12 table-responsive">
            <table class="table table-bordered align-middle  mt-3 overflow-auto">
              <thead class="table-primary">
              <tr>
                <th class="text-center">#</th>
                <th>Service</th>
                <th>Quantity</th>
                <th>Unit Price</th>
                <th>Cost</th>
              </tr>
              </thead>
              <tbody>
              <tr>
                <td v-if="records.length === 0" colspan="5" class="text-center">No record</td>
              </tr>
              <tr class="animate__animated animate__fadeInDown" v-for="(record,index) in records" :key="record.id">
                <td class="text-center">
                  <input type="hidden" :value="record.id">
                  <span class="show-in-print">{{ index+1 }}</span>
                  <i  @click="del(record.id)" class="fa-solid fa-trash-alt text-danger hide-in-print"></i>
                </td>
                <td>{{ record.service.name }}</td>
                <td class="text-end">{{ record.quantity }}</td>
                <td class="text-end">{{ record.service.price }}</td>
                <td class="text-end">{{ record.cost }}</td>
              </tr>
              </tbody>
              <tfoot>
              <tr v-if="records.length > 0">
                <td class="text-end" colspan="2">TotalQty</td>
                <td class="text-end">{{ records.reduce((pv,cv)=>pv+cv.quantity,0) }}</td>
                <td class="text-end">Total</td>
                <td class="text-end">{{ total }}</td>
              </tr>
              </tfoot>
            </table>
          </div>
        </div>


          <div class="row g-2 justify-content-end">
            <div class="col-8 col-md-6 col-lg-4">
              <table class="table table-borderless table-sm ">
                <tbody class="align-middle">
                  <tr class="">
                    <th class="fw-normal text-black-50" style="width: 150px;">Net Amount : </th>
                    <td class="px-3 py-1">{{total}}</td>
                  </tr>
                  <tr>
                    <th class="fw-normal text-black-50">Receipt Amount : </th>
                    <td>
                      <input type="number" v-model="inputReceiptAmount" @keydown="needAmount" class="form-control" placeholder="" min="1" >
                    </td>
                  </tr>
                  <tr>
                    <th class="fw-normal text-black-50">Refund Amount : </th>
                    <td :class="[{errorColor:needPrice},'p-3','py-1']">{{ refundAmount }}<i :class="[{displayShow:needPrice}]" class="fa fa-circle-exclamation ms-1 d-none"></i></td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>



        <div class="row g-2 my-3 py-2 hide-in-print justify-content-center align-items-center">
          <div class="col-6 visually-hidden">
            <input v-model="invoiceNumber" class="form-control">
          </div>
          <div class="col-6">
            <button @click="toPrint()" class="btn btn-primary w-100 text-nowrap">
              <i class="fa-solid fa-print fa-fw"></i>
              Print
            </button>
          </div>
          <div class="col-6">
            <button @click="toPrint()" class="btn btn-primary w-100">
              <i class="fa-solid fa-file fa-fw"></i>
              Save
            </button>
          </div>

        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedService:"",
      inputQuantity:"",
      inputReceiptAmount: " ",
      invoiceId : "",
      cashier : "Win Win Maw",
      services: [
        {
          id:1,
          name : "Web Design",
          price : 100,
        },
        {
          id:2,
          name : "domain service",
          price: 10
        },
        {
          id:3,
          name: "SSL",
          price: 5
        },
        {
          id:4,
          name: "maintenance",
          price: 50
        }
      ],
      recordStart : 1,
      records : [],
      needPrice : false,
      errorColor : 'errorColor',
      displayShow : "displayShow",

    }
  },
  computed: {
    dayAndDate(){
      let months = ["January","February","March","April","May","June","July","August","September","October","November","December"];

      let d = new Date();
      let dateCode = d.getDate()+" "+months[(d.getMonth()+1)]+" "+d.getFullYear()+","+d.getHours()+":"+d.getMinutes();
      return dateCode;
    },
    invoiceNumber() {
      let d = new Date();
      let dateCode = d.getDate()+""+(d.getMonth()+1)+""+d.getFullYear()
      let random = Math.floor(Math.random()*10000);
      this.invoiceId = dateCode+"-"+random;
      return dateCode+"-"+random;
    },
    total(){
      return this.records.reduce((pv,cv)=>pv+cv.cost,0);
    },
    refundAmount(){
      let total = this.records.reduce((pv,cv)=>pv+cv.cost,0);
      let refund = Math.floor((total - this.inputReceiptAmount) * 100)/100;
      return refund;
    },

  },
  methods: {
    saveRecord() {
      let currentService = this.services.find(service=>service.id===this.selectedService);
      let cost = currentService.price * this.inputQuantity;
      let record = {
        id : this.recordStart,
        service : currentService,
        quantity : this.inputQuantity,
        cost
      }
      this.records.push(record);
      this.selectedService = this.inputQuantity = ""
      this.recordStart++
    },
    del(recordId){
      this.records = this.records.filter(record => record.id != recordId)
      console.log(recordId)
    },

    toPrint(){
      print();
    },
    needAmount(){
      let total = this.records.reduce((pv,cv)=>pv+cv.cost,0);
      // let refund = total - this.inputReceiptAmount;
      if(total > this.inputReceiptAmount){
        this.needPrice = true;
        return true;
      }else if (total <= this.inputReceiptAmount){
        this.needPrice = false;
        return false;
      }
    },

  },
}
</script>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Oswald:wght@300;400;700&display=swap');
$font-family-sans-serif : 'Oswald', sans-serif;
$primary: #810fc7;

@import "~bootstrap/scss/bootstrap";
@import "~@fortawesome/fontawesome-free/css/all.min.css";
@import "~animate.css/animate.min.css";

@media screen {
  .show-in-print {
    display: none;
  }
  .errorColor{
    color: orangered !important;
  }
  .displayShow{
    display: inline-block !important;
  }
}
@media print {
  .hide-in-print{
    display: none !important;
  }
  .show-in-print{
    display: block !important;
  }
  input{
    border:none !important;
  }
}
</style>