<template>
  <div class="border">

    <table class="bordered">
    <caption>
      Employees Table
    </caption
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
              <input class="form-control" v-model="employee.name" v-on:input="validateForm(employee)" >
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.position }}</span>
            <span v-if="editIndex === index">
              <input class="form-control" v-model="employee.position" v-on:input="validateForm(employee)">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.salary }}</span>
            <span v-if="editIndex === index">
              <input class="form-control" v-model="employee.salary" v-on:input="validateForm(employee)">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">{{ employee.dateOfBirth }}</span>
            <span v-if="editIndex === index">
              <input  type="date" class="form-control" v-model="employee.dateOfBirth" v-on:input="validateForm(employee)">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">
              <button @click="edit(employee, index)" class="btn" :disabled='isDisabledAdd'>Edit</button>
              <button @click="remove(employee, index)" class="btn" :disabled='isDisabledAdd'>Remove</button>
            </span>
            <span v-else>       
              <button class="btn" @click="cancel(employee)">Cancel</button>
              <button  class="btn" @click="save(employee)" :disabled='isDisabledActions'>Save</button>
            </span>      
            
          </td>
       </tr>
      </tbody>
    </table>
    <div class="btnAddContainer">
      <button @click="add" class="btnAdd" :disabled='isDisabledAdd'>Add employee</button>
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
    disabledActions: false,
    disabledAdd: false,
  }),

   computed: {
  	isDisabledActions: function(){
    	return this.disabledActions;
    },

    isDisabledAdd: function() {
    	return this.disabledAdd;
    },
  },

  mounted() {
    this.getEmployees()
  },
 
  methods: {

    checkDate(employee) {
      let date = new Date(employee.dateOfBirth);
      let now = new Date();
      if(date > now) {
        return true
      } else {
        return false
      }
    },

    validateForm(employee) {   
      if(!employee.position || !employee.name || !employee.salary || !employee.dateOfBirth || this.checkDate(employee)) {
        this.disabledActions = true
      } else {
        this.disabledActions = false
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
      let parsed = JSON.stringify(response);
      localStorage.setItem('employees', parsed);
      this.employees = JSON.parse(localStorage.getItem('employees')).data;
    },

    cancel(employee) {
      this.disabledAdd = false
      this.editIndex = null
      if (!this.originalData) {
        this.remove(employee, this.employees.indexOf(employee))  
        return
      }

      Object.assign(employee, this.originalData)
      this.originalData = null
    },

    edit(employee, index) {  
      this.disabledAdd = true      
      this.originalData = Object.assign({}, employee)
      this.editIndex = index    
    },

    remove(employee, index) {
      this.deleteEmployee(employee.id) 
      this.employees.splice(index, 1)
      localStorage.employees = JSON.stringify(this.employees)
    },

    save(employee) {
      this.disabledAdd = false
      this.patchEmployee(employee, employee.id)  
      localStorage.employees = JSON.stringify(this.employees)
      this.originalData = null
      this.editIndex = null   
    },

    add() { 
      this.disabledAdd = true
      this.originalData = null   
      this.employees.push({ id: this.employees.length + 1, name: '', position: '', salary: '', dateOfBirth: '' })
      this.postEmployee(this.employees[this.employees.lenght])
      this.editIndex = this.employees.length - 1
      this.disabledActions = true
    },
  }, 
}
</script>

<style>

input {
	width: 200px;
	font-size: 13px;
	padding: 6px 0 4px 10px;
	border: 1px solid #cecece;
	background: #F6F6f6;
	border-radius: 10px;
}

table {
  font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
  border-collapse: collapse;
  color: #686461;
}

caption {
  padding: 10px;
  color: white;
  background: #8FD4C1;
  font-size: 18px;
  text-align: left;
  font-weight: bold;
}

th {
  border-bottom: 3px solid #B9B29F;
  padding: 10px;
  text-align: left;
}

tr:nth-child(odd) {
  background: white;
}
tr:nth-child(even) {
  background: #E8E6D1;
}

.btn, .btnAdd {
  border: 0px;
  width: 100%;
  height: 2em;
  margin: 2px;
  background: #8FD4C1;

}

input {outline:none;}

.btn:hover, .btnAdd:hover {   
  background: rgb(53, 167, 110); 
}

.btn:active, .btnAdd:active {
  background: rgb(33,147,90);
  box-shadow: 0 3px rgb(33,147,90) inset;
}

button:disabled,
button[disabled]{
  border: 1px solid #999999;
  background-color: #cccccc;
  color: #666666;
}

</style>
