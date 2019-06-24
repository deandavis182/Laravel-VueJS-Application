<template>
    <div>
        <h2>Customers</h2>
        <br>
        <div style="max-width: 25%; border: 5px dotted;">
        <form @submit.prevent="addCustomer">
          <div>
            <label for="">Name: </label>
            <input style="float: right;" type="text" placeholder="Name" v-model="customer.name">
          </div>
          <hr>
          <div>
            <label for="">Address: </label>
            <input style="float: right;" type="text" placeholder="Address" v-model="customer.address">
          </div>
          <hr>
          <div>
            <label for="">Phone: </label>
            <input style="float: right;" type="tel" placeholder="Phone" v-model="customer.phoneNumber">
          </div>
          <hr>
          <div>
            <label for="">Email: </label>
            <input style="float: right;" type="email" placeholder="Email" v-model="customer.emailAddress">
          </div>
          <hr>
          <div>
            <label for="">Contract Start Date: </label>
            <input style="float: right;" type="date" placeholder="Contract Start" v-model="customer.contractStartDate">
          </div>
          <hr>
          <div>
            <label for="">Contract Length (weeks) </label>
            <input style="float: right;" type="number" placeholder="Contract Length (weeks)" min="1" v-model="customer.contractLength">
          </div>
          <br>
          <button type="submit">Save</button>
        </form>
        </div>
        <br>
        <nav>
          <ul>
            <li v-bind:class="[{disabled: !pagination.prev_page_url}]">
              <a href="#" @click="getCustomers(pagination.prev_page_url)">Previous</a>
            </li>
            <li class="disabled">
              <a href="#" style="color: black; text-decoration: none;">Page {{ pagination.current_page }} of {{ pagination.last_page }}</a>
            </li>
            <li v-bind:class="[{disabled: !pagination.next_page_url}]">
              <a href="#" @click="getCustomers(pagination.next_page_url)">Next</a>
            </li>
          </ul>
        </nav>
        <br>
        <div style="border: 5px double; border-width: thick; max-width: 25%; margin-bottom: 1em;" v-for="customer in customers" v-bind:key="customer.id">
          <h3>{{ customer.customer }}</h3>
          <p>{{ customer.email_address }}</p>
          <hr>
          <button @click="deleteCustomer(customer.id)">Delete</button>
        </div>
    </div>
</template>

<script>
  export default {
    data() {
      return {
        customers: [],
        customer: {
          id: '',
          name: '',
          address: '',
          phoneNumber: '',
          emailAddress: '',
          startDate: '',
          contractStartDate: '',
          contractLength: ''
        },
        customer_id: '',
        pagination: {},
        edit: false
      }
    },

    created() {
      this.getCustomers();   // created() runs when page is loaded
    },

    methods: {
      getCustomers(page_url) {
        let vm = this;
	      page_url = page_url || 'http://wallsworth-api-test.sapphirebd.com/api/customers'
        fetch(page_url)
          .then(res => res.json())
          .then(res => {
            //console.log(res.data);
            this.customers = res.data;
            vm.makePagination(res.meta, res.links);
          })
          .catch(err => console.log(err));
      },
      makePagination(meta, links) {
        let pagination = {
          current_page: meta.current_page,
          last_page: meta.last_page,
          next_page_url: links.next,
          prev_page_url: links.prev 
        }
        this.pagination = pagination;
      },
      deleteCustomer(id) {
        if(confirm('Are you sure?')) {
          fetch('http://wallsworth-api-test.sapphirebd.com/api/customer/'+id, {
            method: 'delete'
          })
          .then(res => res.json())
          .then(data => {
            alert('Customer Removed');
            this.getCustomers();
          })
          .catch(err => console.log(err));
        }
      },
      addCustomer() {
        if ( this.edit === false) {
          // add
          fetch('http://wallsworth-api-test.sapphirebd.com/api/customer', {
            method: 'post',
            body: JSON.stringify(this.customer),
            headers: {
              'content-type': 'application/json'
            }
          })
          .then(res => res.json())
          .then(data => {
            this.customer.name = '';
            this.customer.address = '';
            this.customer.phoneNumber = '';
            this.customer.emailAddress = '';
            this.customer.startDate = '';
            this.customer.contractStartDate = '';
            this.customer.contractLength = '';
            alert('Customer Added!');
            this.getCustomers();
          })
          .catch(err => console.log(err));
        } else {
          // update
        }
      }
    }
  }
</script>
