<template>
    <div class="wrapper">
        <div class="color__input">
            <input v-model="url" placeholder="https://example.com">
            <button @click="get()">OK</button>
        </div>
        <br />
        <img src="../assets/ajax-loader.gif" v-if="show">
        <img  class="main_img" v-bind:src="url" v-show="table['colors']"/>
        <div class="color__list">
            <div class="color__list_item" v-show="table['colors']" v-for="item in table['colors']" v-bind:key="item">
                <div class="color__item_picker" v-bind:style="{ backgroundColor: item }"></div>
                    {{ item }}
                
            </div>
        </div>
            
    </div>
</template>

<script>

import Axios from 'axios';
//import { debounce } from 'lodash';

export default {
    name : 'Main',
    data() {
        return {
            url : '',
            show : false,
            table : []
        }
    },
    methods : {
    get : function() {
            if ( this.url == '' || this.url.indexOf('http') == -1) {
                alert('Add url');
                this.url = '';
                this.table = [];
            } else {
            this.show = !this.show;
            Axios.get(`https://xerro.getopinion.pl/mail_gen/harvester/?url=${this.url}`).then((result)=>{
                this.table = result.data
                this.show = !this.show;
            })
            }
    }
}
}
</script>

<style scoped>

.wrapper {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin-bottom: 50px;
}

.color__input {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
}

.color__input input {

    width: 500px;
    border: 0;
    border-bottom: 1px solid #000000;
    padding: 10px 5px 10px 5px;
    font-family: Arial, Helvetica, sans-serif;
    font-size: 17px;
    outline: none;
    transform: all .6s ease-in;
}

.color__input input:focus {
    border-bottom: 1px solid #800000;
}

.color__input button {
    width: 150px;
    padding: 10px 20px 10px 20px;
    margin: 5px;
    font-family: Arial, Helvetica, sans-serif;
    font-size: 17px;
    color: #fff;
    outline: none;
    background-color: #800000;
    border: 1px solid #fff;
    border-radius: 15px;
    transition: all .1s ease-in;
}

.color__input button:hover {
    cursor: pointer;
    background-color: #fff;
    color: #800000;
    border: 1px solid #800000;
}

.color__list {
    max-width: 600px;
    display: block;
    float: left;
}

.main_img {
    max-width: 300px;
    margin: 20px 0px 20px 0px;
}

.color__list_item {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    float: left;
    width: 190px;
    margin: 5px;
    font-family: Arial, Helvetica, sans-serif;
    font-weight: 600;
}

.color__item_picker {
    width: 50px;
    height: 50px;
    margin: 10px;
}

</style>