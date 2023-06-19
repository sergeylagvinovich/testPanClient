<template>
  <div class="row">
    <div class="col-12">
      <div class="row justify-content-end">
        <div class="col-2">
          <input type="email" :class="['form-control',error('email')?'error':'']" v-model="email" id="email_input" placeholder="Введите email" >
        </div>
        <b-tooltip target="email_input" variant="danger" v-if="error('email')" triggers="hover" >
          <ul class="list-group text-left">
            <li class="list-inline-item" v-for="(error,index) in errors['email']">{{index+1+'. '+error}}</li>
          </ul>
        </b-tooltip>
        <div class="col-2">
          <input type="number" :class="['form-control',error('pan')?'error':'']" v-model="pan" id="unp_input" placeholder="Введите унп">
          <b-tooltip target="unp_input" variant="danger" v-if="error('pan')" triggers="hover">
            <ul class="list-group text-left">
              <li class="list-inline-item" v-for="(error,index) in errors['pan']">{{index+1+'. '+error}}</li>
            </ul>
          </b-tooltip>
        </div>
        <div class="col-1">
          <b-button variant="primary" @click="checkOrganization">
            <b-spinner small v-if="loading"></b-spinner>
            <span v-else>Проверить</span>
          </b-button>
        </div>
      </div>
      <div class="row mt-3">
        <table class="table">
          <thead class="bg-primary text-light">
          <tr>
            <th scope="col">#</th>
            <th scope="col">УНП</th>
            <th scope="col">Есть в локально базе</th>
            <th scope="col">Есть в сторонней базе</th>
          </tr>
          </thead>
          <tbody>
          <TableRow v-for="(item,index) in unps" :index="index" :item="item"></TableRow>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>

import TableRow from "./TableRow";
import axios from 'axios';
import {isEmpty, isNull} from "lodash";
export default {
  name: 'HomePage',
  components: {TableRow},
  data() {
    return {
      target:null,
      errors:{},
      pan:null,
      email:null,
      loading:false,
      unps: []
    }
  },
  methods:{
    error(value){
      let err = this.errors;
      if(isEmpty(err)){
        return false;
      }
      return !isEmpty(err[value]);
    },
    checkOrganization(){
      this.loading = true;
      this.errors = {};
      axios.defaults.headers.post['Content-Type'] ='application/json;charset=utf-8';
      axios.defaults.headers.post['Access-Control-Allow-Origin'] = '*';
      axios.post('http://localhost:5271/Home/checkOrganization',{
        pan: Number(this.pan),
        email: String(this.email)
      }).then((resp)=>{
        let data = resp.data;
        let exist = this.unps.filter((x)=>x.pan === resp.data.pan).length>0;
        if(exist){
          let index = this.unps.findIndex((x=>x.pan === resp.data.pan));
          this.unps.splice(index,1,data);
        }else
        {
          this.unps.push(data);
        }

      }).catch((err)=>{
        if(err.response.status===400) {
          this.errors = err.response.data.errors;
        }
      }).finally(()=>{
        this.loading = false;
      })

    },
  },
}
</script>

<style>
@import 'bootstrap/dist/css/bootstrap.css';
@import 'bootstrap-vue/dist/bootstrap-vue.css';

.error{
  border-color: #dc3545 ;
}
</style>
