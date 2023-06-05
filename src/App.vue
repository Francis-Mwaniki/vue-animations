<template>
  <div>
  <!-- navs parralax, clound, phone   links-->
  <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
      
      <button
        class="navbar-toggler"
        type="button"
        data-bs-toggle="collapse"
        data-bs-target="#navbarNav"
        aria-controls="navbarNav"
        aria-expanded="false"
        aria-label="Toggle navigation"
      >
      <span class="navbar-toggler-icon"></span>
      </button>
      <div class="collapse navbar-collapse" id="navbarNav">
      <ul class="navbar-nav">
        <li class="nav-item">
        <a class="nav-link active" aria-current="page"  @click="parax=true; cloud=false; phone=false ">Parallax</a>
        </li>
        <li class="nav-item">
        <a class="nav-link"  @click="parax=false; cloud=false; phone=true">Phone</a>
        </li>
       
      </ul>
      </div>
    </div>
  </nav>
<div class="flexed">
  <notification/>
  <Alerts/>
  <IndividualAlerts/>
</div>

  <div v-if="phone">
<Phone/>
</div>  
<div v-if="parax">
  <Parralax  />
</div>
<div v-if="cloud">
<Cloud/>
</div>

  </div>
</template>

<script>
import Parralax from './components/Parralax.vue';
import Cloud from './components/Cloud.vue';
import Phone from './components/Phone.vue';
import Alerts from './components/Alerts.vue';
import notification from './components/notification.vue';
import IndividualAlerts from './components/Individual.alert.vue';

  export default {
    components: { Parralax, Cloud, Phone, notification, Alerts, IndividualAlerts },
    data() {
      return {
        parax: false,
        cloud: false,
        phone: false,
      };
    },
}
</script>

<style   scoped>
/* Custom styles for the navbar */
.navbar {
  background-color: #343a40;
}

.navbar-brand {
  font-weight: bold;
}

.flexed{
  display: flex;
  justify-content: space-around;
  align-items: center;
  flex-wrap: wrap;
  gap: 20px;
}
.navbar-toggler {
  border: none;
}

.nav-link {
  color: #fff;
}

.nav-link:hover {
  color: #f8f9fa;
}

.nav-link.active {
  font-weight: bold;
}

.navbar-nav {
  margin-left: auto;
}

.nav-item {
  margin-right: 15px;
}

</style>