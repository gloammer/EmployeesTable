<template>
  <div class="container">

    <table class="table">
      <thead class="thead">
        <tr>
          <th width="1">id</th>
          <th width="10%">Name</th>
          <th width="10%">Position</th>
          <th width="10%">Salary</th>
          <th width="10%">DateOfBirth</th>
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
              <input class="form-control" v-model="employee.dateOfBirth">
            </span>           
          </td>

          <td>
            <span v-if="editIndex !== index">
              <button @click="edit(employee, index)" class="btn">Edit</button>
              <button @click="remove(employee, index)" class="btn">Remove</button>
            </span>
            <span v-else>
              <button class="btn" @click="cancel(employee)">Cancel</button>
              <button class="btn" @click="save(employee)">Save</button>
            </span>
          </td>
       </tr>
      </tbody>
    </table>
    <div class="col-3">
      <button v-show="!editIndex" @click="add" class="btn">Add employee</button>
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
  }),

  mounted() {
    axios.get('http://localhost:3000/employee/')
          .then(response => {
            console.log(response)          
            this.saveEmployee(response)
          })
  },

  methods: {

    deleteEmployee(index) {
      axios.delete('http://localhost:3000/employee/' + index)
        .then(response => console.log(response))
    },

    postEmployee(employee) {
      axios.post('http://localhost:3000/employee/', { name: employee.name  })
        .then(response => console.log(response))
    },

    patchEmployee(employee, index) {
      axios.patch('http://localhost:3000/employee/' + index, { 
          name: employee.name, position: employee.position, salary: employee.salary, dateOfBirth: employee.dateOfBirth  
        })
        .then(response => console.log(response))
    },

    saveEmployee(response) {
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
      this.originalData = Object.assign({}, employee)
      this.editIndex = index
    },

    remove(employee, index) {
      this.deleteEmployee(index)
      this.employees.splice(index, 1)
    },

    save(employee) {    
      this.patchEmployee(employee, this.editIndex)   
      this.originalData = null
      this.editIndex = null   
    },

    add() {  
      this.originalData = null   
      this.employees.push({ id: this.editIndex, name: '', position: '', salary: '', dateOfBirth: ''  })
      this.postEmployee(this.employees)
      this.editIndex = this.employees.length - 1        
    },
  }, 
}
</script>

<style>
 input[type="number"] {
    text-align: right;
  }
</style>
