<template>

<div id="app"> 
   
  <button v-on:click="check" >check</button>
  <button class="btn" v-on:click="addEmployee" >Toggle Enable</button>
 
  <pre>
    {{ name  }}   
  </pre>

  <li v-for="item in employeesList" :key="item.id">
    

    {{ item }}
    
     
    <input class="text" type="text" :id="item.id"> 
    <button class="btn" v-on:click="check(item.id - 1)">edit</button>
  
  </li>
  
  <p>
    <input v-model="newEmployee"> 
  </p>
</div>

</template>

<script>

import axios from 'axios'

export default {
    data: () => ({
      /*
      employee: {
        name: '',
        position: '',
        salary: '',
        dateOfBirth: Date
      },
      */
      employee: '',
      name: '',
      employeesList: [],
      newEmployee: ''
    }),

    mounted() {
       axios.get('http://localhost:3000/Employee/')
          .then(response => {
            console.log(response)
            this.saveEmployee(response)
          })
    },

    methods: { 

      check(i) {
        //console.log(JSON.parse(localStorage.employees).data)
        //console.log(this.employees)
        let b = document.body.getElementsByClassName('btn')[i]
        let t = document.body.getElementsByClassName('text')[i]
    
        if(t.disabled) {
          t.disabled = false;      
        } 
        else t.disabled = true;   
      },

      removeEmployee(employee) {

      },

      addEmployee() {    
        if (!this.newEmployee) {
          return;
        }

        this.employeesList.push(this.newEmployee);
        this.newEmployee = '';
        this.saveEmployee(newEmployee)
      },

      saveEmployee(response) {
        let parsed = JSON.stringify(response);
        localStorage.setItem('employees', parsed);
        this.employeesList = JSON.parse(localStorage.getItem('employees')).data;
        
        
      },

      fetchData() {
        axios.get('http://localhost:3000/Employee/')
          .then(response => {
              this.saveEmployee(response)
          })
      }
   }
}
    
</script>

