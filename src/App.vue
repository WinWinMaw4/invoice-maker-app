<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-12 col-md-9 col-lg-8 col-xl-6">
        <div class="min-vh-100 position-relative">
          <figure class="text-center my-0">
            <img src="./assets/icons8-shop-60.png" width="" alt="">
          </figure>

            <h3 class="d-flex justify-content-center align-items-center mb-3 text-primary">
              Invoice Maker
              <a href="" target="_blank" class="hide-in-print ms-2">
                <i class="fa fa-file-circle-plus"></i>
              </a>
            </h3>
            <div class="row justify-content-end text-primary show-in-print">
               <div class="col-12 col-md-6 col-lg-6 text-end small">
                 Phone : 09897654321
               </div>
            </div>

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
                    {{ service.name }}(${{service.price}})
                  </option>
                </select>
              </div>
              <div class="col-4">
                <input type="number" v-model="inputQuantity" class="form-control" placeholder="Quantity" min="1" required>
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
                <thead class="table-primary align-middle">
                <tr class="text-center">
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
                <Row v-for="(record,index) in records" :key="record.id" :record="record" :index="index" @to-del="del(record.id)"></Row>
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
            <div class="col-8 col-md-6 col-lg-6">
              <table class="table table-borderless table-sm ">
                <tbody class="align-middle">
                <tr class="">
                  <th class="fw-normal text-black-50" style="width: 150px;">Net Amount : </th>
                  <td class="px-3 py-1">{{total}}</td>
                </tr>
                <tr>
                  <th class="fw-normal text-black-50">Receipt Amount : </th>
                  <td>
                    <input type="number" v-model="inputReceiptAmount" @keydown="needAmount" class="form-control w-100" placeholder="" min="1" >
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



         <div class="position-absolute border p-3 rounded hide-in-print bg-white w-100" style="bottom: 10px">
           <div class="row">
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
    </div>
  </div>
</template>

<script>
import Row from "@/components/Row";
export default {
  components:{Row},
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
      // recordId
      console.log("listen event from child component")
      this.records = this.records.filter(record => record.id != recordId);
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
//@import "~bootstrap/dist/css/bootstrap.min.css";
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