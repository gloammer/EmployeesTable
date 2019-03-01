<template>

<div id="app"> 
  
  <button v-on:click="fetchData" @click="disabled = (disabled + 1) % 2">Toggle Enable</button>
  <input type="text" id="name" name="name"  v-model="name" :disabled="disabled == 1 ? true : false">

  <pre>

    {{ name  }}
    
  </pre>
  

</div>
    
</template>

<script>


import axios from 'axios'
export default {
    data: () => ({
    name: '',
    disabled: 0,

    }),

    methods: { 
     fetchData() {
       axios.patch('http://localhost:3000/Employee/1', {email: "2323"})
         .then(result => console.log(result.data) )
     }
   }
}
    
</script>

