<template>
  <div id="container">
    <transition name="fade">
      <div
        v-show="loading"
        class="loading" 
      >
        <span class="fa fa-spinner fa-spin" /> Loading...
      </div>
    </transition>

    <transition name="fade">
      <div
        v-show="end"
        class="end" 
      >
        <span class="fa fa-spinner fa-spin" /> No more data found
      </div>
    </transition>

    <div class="header">
      <h1>Vue Js Infinite Loading Excercise</h1>
    </div>
    <div class="filter">
      <input 
        v-model="filter" 
        placeholder="Filter by Mission name"
      >
    </div>
    <div id="infinite-scroll">
      <div 
        v-for="launch in filteredLaunchData" 
        :key="launch.flight_number"
        :style="{ backgroundImage: 'url(' + launch.links.mission_patch + ')' }"
        class="list"
      >
        <div class="details">
          <p>
            <b>Mission name:</b> 
            <br>
            {{ launch.mission_name }}
            <br><br>
            <b>Launch year:</b>
            <br>
            {{ launch.launch_year }}
            <br><br>
            <b v-if="launch.details">Details:</b>
            <br>
            {{ launch.details }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'MainComponent',
  data() {
    return {
      loading: false,
      end: false,
      launchData: [],
      filter: '',
      offset: 0,
      limit: 3
    };
  },
  computed: {
    filteredLaunchData() {
      return this.launchData.filter((item) => {
        return item.mission_name.toLowerCase().includes(this.filter.toLowerCase());
      });
    }
  },
  beforeMount() {
    this.getInitialLaunchData();
  },
  mounted() {
    this.getNextLaunchData();
  },
  methods: {
    incrementOffset() {
      this.offset += this.limit;
    },
    getInitialLaunchData() {
      this.loading = true;
      axios.get('https://api.spacexdata.com/v3/launches?id=true&limit=' + this.limit + '&offset=' + this.offset).then((response) => {
        this.launchData = response.data;
        this.incrementOffset();
        this.loading = false;
      });
    },
    getNextLaunchData() {
       // Detect when scrolled to bottom.
      const listElm = document.querySelector('#infinite-scroll');
      listElm.addEventListener('scroll', () => {
        if(listElm.scrollTop + listElm.clientHeight >= listElm.scrollHeight) {
           this.loading = true;
           axios.get('https://api.spacexdata.com/v3/launches?id=true&limit=' + this.limit + '&offset=' + this.offset).then((response) => {
            
            if(response.data.length === 0){
              this.loading = false;
              this.end = true;
              setTimeout(() => {
                this.end = false;
              }, 500);
            } else {
              response.data.map((item) => {
                this.launchData.push(item);
              });
              this.incrementOffset();
              this.loading = false;
            }
          });
        }
      });
    },
  }
};

</script>

<style scoped>
  .header {
    text-align:center;
  }
  #infinite-scroll {
    width: 30%;
    height: 80vh;
    margin-top: 25px;
    margin-left: 35%;
    margin-right: 35%;
    background-color: #EAEAEA;
    overflow:auto;
    border-radius: 10px;
  }

  .list {
    border-radius: 5px;
    padding: 25px;
    margin-left: 5%;
    margin-right: 5%;
    margin-top: 25px;
    margin-botton: 25px;
    height: auto;
    width: 90%;
    background-color: #FFF;
  }

  .loading {
    text-align: center;
    position: absolute;
    color: #fff;
    z-index: 1000;
    background: #000;
    padding: 8px 18px;
    border-radius: 5px;
    left: calc(50% - 45px);
    top: calc(50% - 18px);
  }

  .end {
    text-align: center;
    position: absolute;
    color: #fff;
    z-index: 1000;
    background: #000;
    padding: 8px 18px;
    border-radius: 5px;
    left: calc(50% - 45px);
    top: calc(50% - 18px);
  }

  .details {
    background-color: rgba(255, 255, 255, 0.8);
    font-size: 12px;
    font-family: "Helvetica";
    border-radius: 3px;
    width: 100%;
    height: 100%;
    padding: 10px;
    color: #000;
  }

  .filter {
    text-align:center;
    font-family: "Helvetica";
  }

  input {
    border-radius: 3px;
    border: 0px;
    width: 300px;
    height: 40px;
    font-size: 12px;
    padding: 2px;
  }

  input:focus {
       outline: none !important;
    }
</style>