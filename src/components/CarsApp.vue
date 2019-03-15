<template>
  <div class="container pt-5" id="main">
    <div class="row mb-4">
      <div class="col">
        <h1>{{ msg }}</h1>
      </div>
    </div>
    <div class="row">
      <div class="col-md-4">
        <div class="form-group">
          <label for="search">Find your car</label>
          <input
            type="text"
            class="form-control"
            id="search"
            placeholder="Find your car"
            v-model="search"
          />
        </div>
        <hr />
        <ul class="list-group">
          <li
            v-for="(car,index) of filteredCards"
            :key="car.index"
            @click="selectCar(car, index)"
            class="list-group-item list-group-item-action"
            v-bind:class="{'active' : selectedCarIndex === index}"
          >
            {{ car.name }} - {{ car.model }}
          </li>
        </ul>
      </div>
      <div class="col-md-8 pt-3">
        <div class="row">
          <div class="col-md-5">
            <h2>{{car.name}}</h2>
            <img 
            :src="car.image"
            v-bind:alt="car.name"          
            class="rounded detailed-car-image" 
             />
          </div>
          <div class="col-md-7">
            <h2>Car details</h2>
            <ul class="list-group pb-5">
              <li>{{car.model}}</li>
              <li>{{car.year}}</li>
              <li>{{car.owner}}</li>
            </ul>
            <transition name="phone">
            <p v-if="visability">{{car.phone}}</p>
            </transition>
            <button class="btn btn-outline-success mr-2" v-on:click="visability=!visability">{{phoneBtnText}}</button>
            <button class="btn btn-primary" @click="modalVisability=true">Buy</button>
          </div>
        </div>
      </div>
    </div>
<transition name="modal">
    <div class="modal fade show" id="see" tabindex="-1" role="dialog" v-if="modalVisability">
  <div class="modal-dialog" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Do you want to buy the car?</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close" @click="modalVisability=false">
          <span aria-hidden="true">&times;</span>
        </button>
      </div>
      <div class="modal-body">
        <p>{{car.name}}</p>
            <ul>
              <li>{{car.model}}</li>
              <li>{{car.year}}</li>
              <li>{{car.owner}}</li>
            </ul>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-dismiss="modal" @click="cancelOrder">Cancel</button>
        <button type="button" class="btn btn-primary" @click="newOrder">Buy</button>
      </div>
    </div>
  </div>
</div>
</transition>
<transition name="modal-back"><div class="modal-backdrop fade show" v-if="modalVisability"></div></transition>
 
     <div class="row pt-5" v-if="logs.length" >
       <div class="col-md-12">
      <ul>
        <li 
        v-for="log in logs" 
        :key="log.index"
        :class="{
           'list-group-item list-group-item-secondary' : log.type === 'cancel',
          'list-group-item list-group-item-success' : log.type === 'ok'
        }"
        >
        {{log.text}} {{log.date | date}}
        </li>
      </ul>
      </div>
    </div>
   </div>
</template>

<script>
import ford from '../images/1.jpg';
import sport from '../images/porsche.png';
import porsche from '../images/panamera.jpg';

const car = (name, model, owner, year, phone, image) => ({
    name,
    model,
    owner,
    year,
    phone,
    image
});
const cars = [
    car("Ford", "Focus", "Roma", "2016", "+7 936 362 22 45", ford),
    car("Porsche", "Sport", "Maya", "2015", "+7 522 362 22 45", sport),
    car("Porsche", "Panamera", "Lisa", "2018", "+7 452 369 22 45", porsche)

];
const log = (text, type, date=new Date()) => ({ text, type, date });

export default {
    name: "CarsApp",
    props: {
        msg: String
    },
    data() {
        return {
            cars,
            car:cars[0],
            selectedCarIndex: 0,
            visability: false,
            search: '',
            modalVisability: false,
            logs: []
        };
    },
    methods: {
        selectCar(car, index){
            this.car = car;
            this.selectedCarIndex = index;
        },
        newOrder(){
            this.modalVisability = false;
            this.logs.push(
                log(`Success order: ${this.car.name} - ${this.car.model}`, "ok")
            );
        },
        cancelOrder(){
            this.modalVisability = false;
            this.logs.push(
                log(`Cancel order: ${this.car.name} - ${this.car.model}`, 'cancel')
            ); 
        }
    },
    computed: {
        phoneBtnText(){
            return this.visability ? 'Hide phone' : 'Show phone';
        },
        filteredCards(){
            let searchTemplate = this.search.toLowerCase();
            return this.cars.filter( car => car.name.toLowerCase().includes(searchTemplate) || car.model.toLowerCase().includes(searchTemplate));
        // return this.cars.filter( car => car.name.indexOf(this.search) > -1 || car.model.indexOf(this.search) > -1);
        },
        filters: {
            date(value){
                return value.toLocaleString();
            }
        }
    }
};
</script>
 
 