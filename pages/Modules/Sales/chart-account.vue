<template>
    <div id="layoutSidenav">
      <CommonSidebar />
      <div id="layoutSidenav_content">
        <CommonHeader />
        <main>
          <div class="container-fluid">
           
              <div>
              <img class="center" height="50%" width="50%" src="~/assets/images/comingsoon.png" />
            </div>
          </div>
        </main>
      </div>
    </div>
  </template>
  <script>
  import CommonHeader from '../../../components/CommonHeader.vue';
  import CommonSidebar from '../../../components/CommonSidebar.vue';
  export default {
    components: { CommonSidebar, CommonHeader },
    name: "chart-account",
    
    
    data() {
      return {
       
      };
    },
  
    mounted() {
     
    },
    beforeMount() {
     
  
    },
    methods: {
    
    }
  };
  </script>
  <style scoped>

  
  </style>
  