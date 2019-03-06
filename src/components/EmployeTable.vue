<template>
  <div class="border">

    <table class="bordered">
      <thead class="thead">
        <tr>
          <th width="1">id</th>
          <th width="30%">Name</th>
          <th width="30%">Position</th>
          <th width="30%">Salary</th>
          <th width="30%">DateOfBirth</th>
          <th width="100%">Actions</th>
        </tr>
      </thead>
      <tbody>

        <tr v-for="(employee, index) in employees" :key="index">
          <td>{{ employee.id }}</td>

          <td>
            <span v-if="editIndex !== index">{{ employee.name }}</span>
            <span v-if="editIndex === index">
              <input class="form-control" v-model="employee.name">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.position }}</span>
            <span v-if="editIndex === index">
              <input class="form-control" v-model="employee.position">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.salary }}</span>
            <span v-if="editIndex === index">
              <input class="form-control" v-model="employee.salary">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.dateOfBirth }}</span>
            <span v-if="editIndex === index">
              <input  type="date" class="form-control" v-model="employee.dateOfBirth">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">
              <button @click="edit(employee, index)" class="btn">Edit</button>
              <button @click="remove(employee, index)" class="btn">Remove</button>
            </span>
            <span v-else>
             
              <button class="btn" @click="cancel(employee)">Cancel</button>
              <button class="btn" @click="save(employee)" v-bind:disabled="validated">Save</button>
            </span>      
            
          </td>
       </tr>
      </tbody>
    </table>
    <div class="btnAddContainer">
      <button @click="add" class="btnAdd">Add employee</button>
    </div>
  </div> 
</template>

<script>

import axios from 'axios'

export default {

  name: 'employeesTable',

  data: () => ({
    editIndex: null,
    originnalData: null,
    employees: [],  
    validated: false,
  }),

  mounted() {
    this.getEmployees()
  },
 
  methods: {

    validateForm(employee) {
      console.log(employee.name)
      if(employee.name === ''){
        this.validated = false
      }
    },

    getEmployees() {
      axios.get('http://localhost:3000/employee/')
          .then(response => { this.saveEmployee(response) })
    },

    deleteEmployee(index) {
      axios.delete('http://localhost:3000/employee/' + index)
        .then(response => {  })
    },

    postEmployee(employee) {
      axios.post('http://localhost:3000/employee/', { employee })
        .then(response => {  })
    },

    patchEmployee(employee, index) {
      axios.patch('http://localhost:3000/employee/' + index, { 
          name: employee.name, position: employee.position, salary: employee.salary, dateOfBirth: employee.dateOfBirth  
        })
        .then(response => { })
    },

    saveEmployee(response) {
      console.log(response)
      let parsed = JSON.stringify(response);
      localStorage.setItem('employees', parsed);
      this.employees = JSON.parse(localStorage.getItem('employees')).data;
    },

    cancel(employee) {
      this.editIndex = null
      if (!this.originalData) {
        this.employees.splice(this.employees.indexOf(employee), 1)
        return
      }

      Object.assign(employee, this.originalData)
      this.originalData = null
    },

    edit(employee, index) {
      this.validateForm(employee) 
      console.log(this.employees)
      this.originalData = Object.assign({}, employee)
      this.editIndex = index    
    },

    remove(employee, index) {
      this.deleteEmployee(employee.id)
      this.employees.splice(index, 1)
    },

    save(employee) {   
      
      this.patchEmployee(employee, employee.id)  
      this.originalData = null
      this.editIndex = null   
    },

    add() {  
      this.originalData = null   
      this.employees.push({ id: this.employees.length + 1, name: '', position: '', salary: '', dateOfBirth: ''  })
      this.postEmployee(this.employees[this.employees.lenght])
      this.editIndex = this.employees.length - 1
    },
  }, 
}
</script>

<style>




table {
 
    overflow:hidden;
    border:1px solid #d3d3d3;
    background:#fefefe;
    width:70%;
    margin:5% auto 0;
    -moz-border-radius:5px; 
    -webkit-border-radius:5px; 
    border-radius:5px;
    -moz-box-shadow: 0 0 4px rgba(0, 0, 0, 0.2);
    -webkit-box-shadow: 0 0 4px rgba(0, 0, 0, 0.2);
}
 
th, td {
    padding:10px;
    text-align:center; 
}
 
th {
    padding-top:10px;
    text-shadow: 1px 1px 1px #fff;
    background:#e8eaeb;
}
 
td {
    border-top:1px solid #e0e0e0; 
    border-right:1px solid #e0e0e0;
}
 
tr.odd-row td {
    background:#f6f6f6;
}
 
td.first, th.first {
    text-align:left
}
 
td.last {
    border-right:none;
}
 
td {
    background: -moz-linear-gradient(100% 25% 90deg, #fefefe, #f9f9f9);
   
}
 
tr.odd-row td {
    background: -moz-linear-gradient(100% 25% 90deg, #f6f6f6, #f1f1f1);
  
}
 
th {
    background: -moz-linear-gradient(100% 20% 90deg, #e8eaeb, #ededed);
  
}
 
tr:first-child th.first {
    -moz-border-radius-topleft:5px;
    -webkit-border-top-left-radius:5px; 
}
 
tr:first-child th.last {
    -moz-border-radius-topright:5px;
    -webkit-border-top-right-radius:5px; 
}
 
tr:last-child td.first {
    -moz-border-radius-bottomleft:5px;
    -webkit-border-bottom-left-radius:5px; 
}
 
tr:last-child td.last {
    -moz-border-radius-bottomright:5px;
    -webkit-border-bottom-right-radius:5px; 
}

.btn, .btnAdd {

  
  font-weight: 700;
  color: white;
  text-decoration: none;
  padding: .8em 1em calc(.8em + 3px);
  border-radius: 3px;
  background: rgb(64,199,129);
  box-shadow: 0 -3px rgb(53,167,110) inset;
  transition: 0.2s;
} 

.btn:hover, .btnAdd:hover { background: rgb(53, 167, 110); }

.btn:active, .btnAdd:active {
  background: rgb(33,147,90);
  box-shadow: 0 3px rgb(33,147,90) inset;
}

.btnAddContainer {
  right: 500px
}

button:disabled,
button[disabled]{
  border: 1px solid #999999;
  background-color: #cccccc;
  color: #666666;
}


</style>
