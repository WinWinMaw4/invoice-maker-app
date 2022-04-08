<template>
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-12 col-md-9 col-lg-8 col-xl-6">
        <div class="min-vh-100 position-relative">
          <figure class="text-center my-0">
            <img src="./assets/icons8-shop-60.png" width="" alt="">
          </figure>

            <h3 class="d-flex justify-content-center align-items-center mb-3 text-primary">
              <span style="text-shadow:0px 3px 3px white ">Invoice Maker</span>
              <a href="" target="_blank" class="hide-in-print ms-2">
                <i class="fa fa-file-circle-plus"></i>
              </a>
            </h3>
            <div class="row justify-content-end text-primary show-in-print">
               <div class="col-12 col-md-6 col-lg-6 text-end small">
                 Phone : 09897654321
               </div>
            </div>

          <div class="row align-items-center text-light fw-light my-2 my-md-4">
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
                <select v-model="selectedService" class="form-select ">
                  <option value="" >Select Service</option>
                  <option v-for="service in services" :value="service.id" :key="service.id" class="op">
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
            <div class="col-12 table-responsive scroll-style">
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
              <table class="table table-borderless table-sm">
                <tbody class="align-middle">
                <tr class="">
                  <th class="fw-normal" style="width: 150px;">Net Amount : </th>
                  <td class="px-3 py-1 ">{{total}}</td>
                </tr>
                <tr>
                  <th class="fw-normal">Receipt Amount : </th>
                  <td class="text-end" style="width: fit-content">
                    <input type="number" v-model="inputReceiptAmount" @change="needAmount" class="form-control d-block w-100" style="width: fit-content;" placeholder="" min="1" >
                  </td>
                </tr>
                <tr>
                  <th class="fw-normal " >Refund Amount : </th>
                  <td :class="[{errorColor:needPrice},'p-3 py-1']">{{ refundAmount }} <span :class="[(needPrice==true) ? 'd-inline-block':'d-none','ms-1']">need money<i  class="fa fa-circle-exclamation" ></i></span></td>
                </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div class="text-center show-in-print text-primary small p-3 position-absolute" style="bottom: 10%" >
            Invoice Maker မှဝယ်ယူအားပေးမှုအတွက်လှိုက်လှဲစွာကျေးဇူးတင်ရှိပါသည်
          </div>
         <div class="position-absolute border p-3 rounded hide-in-print w-100" style="bottom: 10px">
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
      needPrice : false ,
      errorColor : 'errorColor',
      isDel:false,

    }
  },
  computed: {
    dayAndDate(){
      let months = ["January","February","March","April","May","June","July","August","September","October","November","December"];

      let d = new Date();
      let dateCode = d.getDate()+" "+months[(d.getMonth()+1)]+" "+d.getFullYear()+", "+d.getHours()+":"+d.getMinutes();
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
      // let total = this.records.reduce((pv,cv)=>pv+cv.cost,0);
      let total = this.total;
      let refund = Math.abs(Math.floor((total - this.inputReceiptAmount) * 100)/100);
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
      setTimeout(()=>this.records = this.records.filter(record => record.id != recordId),700)
      console.log(recordId)
    },

    toPrint(){
      print();
    },
    needAmount(){
      // let total = this.records.reduce((pv,cv)=>pv+cv.cost,0);
      // let refund = total - this.inputReceiptAmount;
      let total = this.total;
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
$form-select-indicator-color:       $primary;
$table-color:                 #b330ff;
//--bs-table-bg: #e6cff4;

@import "~bootstrap/scss/bootstrap";
//@import "~bootstrap/dist/css/bootstrap.min.css";
@import "~@fortawesome/fontawesome-free/css/all.min.css";
@import "~animate.css/animate.min.css";

@media screen {
  body{
    background-color: #ce9bf5;
    background-image: url("./assets/images/Pink Purple Pink Purple Pink Purple Gradient Background.jpg");
    background-position: bottom;
    background-size: cover;
    //background-image: url("./assets/images/—Pngtree—abstract gradient wave with 3d_3642786.png");
    //background-position: center;
    //background-size: cover;
    background-repeat: no-repeat;
    background-attachment: fixed;
    backdrop-filter: blur(5px);
  }
  img, svg {
    vertical-align: middle;
    filter: drop-shadow(0px 3px 3px white);
  }
  .table-primary{
    --bs-table-bg: #b330ff;
  }
  .form-select {
    color: #ffffff;
    background-color: #b330ff82;
  }
  .form-select>option:hover{
    background: #b330ff82;
  }
  .form-control{
    color: #ffffff;
    background-color: #a445ed;
  }
  .form-control:focus {
    color: #ffffff;
    background-color: #b330ff;
    border-color: #c087e3;
    outline: 0;
    box-shadow: 0 0 0 0.25rem rgb(129 15 199 / 25%);
  }
  .form-select>option:checked,
  .form-select>option:hover {
    background-color:#b330ff  ;
    box-shadow: 0 0 10px 100px #920cff inset ;
  }
  .show-in-print {
    display: none;
  }
  .errorColor{
    color: hotpink !important;
    //font-weight: bold;
    text-shadow: 2px 2px 5px #ff002c;
  }
  tr,th,td{
    color: #fff;
  }




  * ::-webkit-scrollbar-track {
    -webkit-box-shadow: inset 0 0 6px #d084ff;
    border-radius: 10px;
    background-color: #c48af5;
  }
  *  ::-webkit-scrollbar {
    width: 5px;
    height: 5px;
    background-color: #c48af5;
    border-radius: 10px;
  }
  *  ::-webkit-scrollbar-thumb {
    border-radius: 10px;
    -webkit-box-shadow: inset 0 0 6px rgb(196, 138, 245);
    background-color: #810fc7;
  }
  //tbody{
  //  overflow: hidden;
  //}



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
  th{
    color: #810fc7;
  }
  .form-control {
    color: #810fc7;
    //background-color: #a445ed;
  }
}
</style>