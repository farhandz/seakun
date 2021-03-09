<template>
  <main>
    <h1>Order list</h1>
    <div class="wrap-submit">
      <div>
        <input @change="OnChangeData($event)" type="text">
        <button @click="onSubmit()" class="btn-submit">submit</button>
      </div>
      <select v-model="selected">
        <option disabled value="">Please select one</option>
        <option>Ascending</option>
        <option>Descending</option>
      </select>
    </div>
    <div v-if="filterData">
      <div style="text-align: center;" v-if="loading">
        loading....
      </div>
    <div v-else>
      <div  v-for="p in filterByName" :key="p.id" class="wrap-order-list">
      <div class="wrap-header-order">
          <div>
            <span>order id</span>
            <span class="price">{{p.orderId}}</span>
          </div>
          <div>
            <span  class="date">Created at</span>
            <span>10000</span>
          </div>
      </div>
      <div class="wrap-card-body">
        <div>
          <div class="akun">{{p.personalAccount.name}}</div>
          <div class="akun">{{p.personalAccount.email}}</div>
          <div class="akun">{{p.personalAccount.phonenumber}}</div>
        </div>
        <div class="akun-provider">
          <div class="provider-list">
              <div class="provider">
                  <span>provider:</span>
                  <span>{{p.provider}}</span>
              </div>
              <div class="provider">
                  <span>paket:</span>
                  <span>{{p.paket}}</span>
              </div>
              <div class="provider">
                  <span>exp:</span>
                  <span>{{p.expired}}</span>
              </div>
          </div>
        </div>
        <div class="akun">
          <p>Rp.{{p.payment.paymentTotal}}</p>
        </div>
      </div>
      </div>
    </div>
    </div>
    <div v-else>
    <div  v-for="p in paginated" :key="p.id" class="wrap-order-list" @click="OnModalShow(p)">
     <div class="wrap-header-order">
        <div>
          <span>order id</span>
          <span class="price">{{p.orderId}}</span>
        </div>
        <div>
          <span  class="date">Created at</span>
          <span>{{ parseIsoString(p.createdAt) }}</span>
        </div>
     </div>
     <div class="wrap-card-body">
       <div>
         <div class="akun">{{p.personalAccount.name}}</div>
         <div class="akun">{{p.personalAccount.email}}</div>
         <div class="akun">{{p.personalAccount.phonenumber}}</div>
       </div>
       <div class="akun-provider">
         <div class="provider-list">
            <div class="provider">
                <span>provider:</span>
                <span>{{p.provider}}</span>
            </div>
            <div class="provider">
                <span>paket:</span>
                <span>{{p.paket}}</span>
            </div>
            <div class="provider">
                <span>exp:</span>
                <span>{{parseIsoString(p.expired)}}</span>
            </div>
         </div>
       </div>
       <div class="akun">
        <p>Rp.{{(p.payment.paymentTotal)}}</p>
       </div>
     </div>
    </div>
    </div>
   <div class="paginate">
      <button
      :disabled="page === 0"
      @click="prevPage">
      Previous
  </button>
  <button
      :disabled="page >= pageCount - 1"
      @click="nextPage">
      Next
  </button>

  <!-- ini modal  -->
   <transition v-if="showModal" name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">
          <div class="wrap-order-list" @click="OnModalShow(p)">
            <div class="wrap-header-order">
              <div>
                <span>id</span>
                <span class="price">{{orderDetail.orderId}}</span>
              </div>
              <div>
                <span class="date">Created at</span>
                <span>{{orderDetail.createdAt}}</span>
              </div>
            </div>
            <div class="wrap-card-body">
              <div>
                <div class="akun">{{orderDetail.personalAccount.name}}</div>
                <div class="akun">{{orderDetail.personalAccount.email}}</div>
                <div class="akun">{{orderDetail.personalAccount.phonenumber}}</div>
              </div>
              <div class="akun-provider">
                <div class="provider-list">
                  <div class="provider">
                      <span>provider:</span>
                      <span>{{orderDetail.provider}}</span>
                  </div>
                  <div class="provider">
                      <span>paket:</span>
                      <span>{{orderDetail.paket}}</span>
                  </div>
                  <div class="provider">
                      <span>exp:</span>
                      <span>{{parseIsoString(orderDetail.expired)}}</span>
                  </div>
                </div>
              </div>
              <div class="akun">
                <p>Rp.{{orderDetail.payment.paymentTotal}}</p>
              </div>
            </div>
          </div>
            </slot>
          </div>
          <div class="modal-body">
            <slot name="body">
            <div class="wrap-card-body-detail">
              <div>
                <div class="akun-detail">
                  <div>Payment Date:</div>
                  <div style="margin-left: 10px">{{parseIsoString(orderDetail.payment.paymentDate)}}</div>
                </div>
                <div class="akun-detail">
                  <div>Voucher Code:</div>
                  <div>{{orderDetail.voucherCode}}</div>
                </div>
                <div class="akun-detail">
                  <div>Payment Status:</div>
                  <div>{{orderDetail.payment.status}}</div>
                </div>
                <div class="akun-detail">
                  <div>Payment Bank:</div>
                  <div>{{orderDetail.payment.name}}</div>
                </div>
              </div>
            </div>
            </slot>
          </div>
          <div class="modal-footer">
            <slot name="footer">
              <button @click="onCloseModal()" class="modal-default-button">
                OK
              </button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
   </div>
  </main>
</template>
<script>

export default {
  data () {
    return {
      items: [],
      page: 0,
      size: 5,
      searchValue: '',
      loading: false,
      filterData: false,
      selected: '',
      showModal: false,
      orderDetail: {}
    }
  },
  beforeMount () {
    fetch('http://demo2687090.mockable.io/order')
      .then(response => response.json())
      .then(items => {
        console.log(items)
        this.items = items
      })
  },
  computed: {
    filterByName () {
      if (!this.searchValue) {
        return this.paginated
      }
      return this.items.filter(car => {
        return car.personalAccount.name.toLowerCase().indexOf(this.searchValue.toLowerCase()) !== -1
      })
    },
    pageCount () {
      const l = this.items.length
      const s = this.size
      return Math.ceil(l / s)
    },
    paginated () {
      console.log(this.selected)
      const start = (this.page * this.size)
      const end = start + this.size
      return this.items.slice(start, end).sort((a, b) => {
        var bDate = b.createdAt.split(/\D+/)
        var aDate = a.createdAt.split(/\D+/)
        const newB = new Date(Date.UTC(bDate[0], --bDate[1], bDate[2], bDate[3], bDate[4], bDate[5], bDate[6])).toDateString()
        const newA = new Date(Date.UTC(aDate[0], --aDate[1], aDate[2], aDate[3], aDate[4], aDate[5], aDate[6])).toDateString()
        if (this.selected === 'Ascending') {
          return new Date(newA) - new Date(newB)
        } else {
          return new Date(newB) - new Date(newA)
        }
      })

      // if (!this.selected) {
      //   return this.items.slice(start, end)
      // } else {
      //   return this.items.slice(start, end).sort((a, b) => {
      //     return new Date(a.date) - new Date(b.date)
      //   })
      // }
    }
  },
  methods: {
    onCloseModal () {
      this.showModal = false
    },
    OnModalShow (items) {
      console.log(items)
      this.orderDetail = items
      this.showModal = true
    },
    OnChangeData (e) {
      this.searchValue = e.target.value
    },
    parseIsoString (s) {
      // console.log(s)
      var b = s.split(/\D+/)
      return new Date(Date.UTC(b[0], --b[1], b[2], b[3], b[4], b[5], b[6])).toDateString()
    },
    onSubmit () {
      this.loading = true
      this.filterData = true

      setTimeout(() => {
        this.loading = false
      }, 3000)
    },
    nextPage () {
      this.page++
    },
    prevPage () {
      this.page--
    }
  }

}
</script>

<style>

.wrap-submit {
  display: flex;
  justify-content: space-between;
}
.btn-submit {
  border: none;
  padding: 6px;
  margin-left: 10px;
  background: #0971F1;
  outline: none;
  cursor: pointer;
  color: white;
}
input[type="text"] {
  padding: 5px;
}
.wrap-order-list {
  margin-top: 20px;
  border: 1px solid #000000;
}
.wrap-header-order {
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid #000000;

}

.wrap-card-body {
  display: flex;
  justify-content: space-between;
  align-items: center;
  align-content: center;
}

.price {
  margin-left: 10px;
}
.date {
  margin-right: 10px;
}

.akun-provider {
  border-left: 1px solid  #000000;
  height: 100%;
}

.provider {
  display: flex;
  margin-top: 10px;
  width: 100%;
  font-size: 12px;

}

.akun {
  margin-top: 8px;
  margin-bottom: 8px;
  font-size: 12px;
}

.paginate {
  margin-top: 20px;
  text-align: end;
}

.wrap-card-body-detail {
  display: flex;
  justify-content: flex-start;
  border: 1px  solid #000000;
}
.akun-detail {
  display: flex;
  margin-top: 7px;
  justify-content: space-between
}
</style>
