<template>
    <div class="wrapper">
        <div class="color__input">
            <input v-model="url" placeholder="https://example.com">
            <div class="container__ok">
                <button v-if="!showContainer" @click="get(url)">OK</button>
            </div>
            <div v-if="showContainer" class="container__add">
                <button @click="clear()">CLEAR</button>
                <button class="container__add_save" @click="save()" :disabled="!buttonSwitch">{{ buttonSave }}</button>
                <button @click="getPng('.color__list')"> {{ downloadTxt }}</button>
            </div>
        </div>
        <br />
        <img class="loader" src="../assets/ajax-loader.gif" v-if="show">

        <img  class="main_img" v-bind:src="url" v-show="table['colors']"/>
        <div class="color__list" v-bind:id="table['id']">
            <div class="color__list_item" v-show="table['colors']" v-for="item in table['colors']" v-bind:key="item">
                <div class="color__item_picker" v-bind:style="{ backgroundColor: item }"></div>
                    {{ item }}
                
            </div>
        </div>

        <div class="saved__list" v-if="showSaved">

            <div class="saved__list_item" v-for="item in savedTable" v-bind:key="item.added">
                
                <div class="saved__list_download" @click="getPng(`#elem-${item.id}`)">
                    <img src="../assets/download.png" />
                </div>

                <p>{{ decode(item.url).replace(/[a-z]+\:+\/\//g,'').substr(0,20) + '(...)' }}</p>

                <div class="saved__list_item_imgcont">
                    <img :src="decode(item.url)" />
                </div>

                <div class="saved__list_colors" :id="`elem-${item.id}`">

                    <div class="saved__list_colors_element" v-for="elem in JSON.parse(decode(item.colors)).colors.slice(0,4)" v-bind:key="elem">
                        <div class="saved__list_colors_picker" v-bind:style="{ backgroundColor: elem }"></div>
                        {{ elem }}
                    </div>

                </div>

                <button class="saved__list_item_button" @click="showMore(decode(item.url))">MORE</button>

            </div>

        </div>
            
    </div>
</template>

<script>

import Axios from 'axios';
import htmlToImage from 'html-to-image';
import download from 'downloadjs';

export default {
    name : 'Main',
    data() {
        return {
            url : '',
            show : false,
            showContainer : false,
            buttonSave : 'SAVE',
            buttonSwitch : true,
            showSaved : true,
            savedTable : [],
            downloadTxt : 'GET(.PNG)',
            table : []
        }
    },
    methods : {
    get : function(e) {
            if ( e == '' || e.indexOf('http') == -1) {
                alert('Add url');
                this.url = '';
                this.table = [];
            } else {
            this.show = !this.show;
            this.showSaved = false;
            
            Axios.get(`https://xerro.getopinion.pl/mail_gen/harvester/?url=${e}`).then((result)=>{
                this.table = result.data
                this.show = !this.show;
                this.showContainer = !this.showContainer;
            })
            }
    },

    showMore : function(elem) {
            this.buttonSwitch = false;
            this.get(elem);
            this.url = elem;
        
    },

    clear : function() {
            this.showContainer = !this.showContainer;
            this.table = [];
            this.url = '';
            this.buttonSave = 'SAVE';
            this.buttonSwitch = true;
            this.showSaved = 'true';
            this.getSaved();
    },

    save : function() {
            this.buttonSave = 'SAVING!';
            let _url = btoa(this.url);
            let _table = btoa(JSON.stringify(this.table));
            let _string = `https://cors-anywhere.herokuapp.com/http://xerro.getopinion.pl/mail_gen/harvester/post/?url=${_url}&table=${_table}`;
            this.buttonSwitch = false;
            Axios.get(_string).then(()=>{
                this.buttonSave = 'SAVED!';
                this.buttonSwitch = true;
            })
    },

    getSaved : function() {
            Axios.get('https://xerro.getopinion.pl/mail_gen/harvester/api.php').then((result)=>{
                this.savedTable = result.data;
            })
    },

    decode : function(element) {
        return atob(element);
    },

    
    getPng : function(e) {
        this.downloadTxt = 'GENERATE!';
        htmlToImage.toPng(document.querySelector(e))
        .then((dataUrl)=> {
                download(dataUrl, `my-node-${e}.png`);
                this.downloadTxt = 'GET(.PNG)';
        });
    }
    
    },

    created : function() {
        this.showSaved = true;
        this.getSaved();
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

.loader {
    margin-bottom: 20px;
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
    background-color: #fff;
}

.color__item_picker {
    width: 50px;
    height: 50px;
    margin: 10px;
    border: 1px solid #e1e1e1;
}

.container__add_save:disabled {
    opacity: 0.5;
}

.saved__list {
    display: block;
    width: 90%;
    margin: 0px 25px 50px 25px;
}

.saved__list_item {
    position: relative;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    float: left;
    width: 200px;
    margin: 5px;
    border: 1px solid #800000;
    box-shadow: 0px 0px 0px #e1e1e1;
    transition: all .3s ease-in;
    background-color: #fff;
}

.saved__list_item:hover {
    box-shadow: 0px 0px 20px #800000;
}

.saved__list_item p {
    font-size: 12px;
    font-weight: 600;
}

.saved__list_item_imgcont {
    width: 100%;
    height: 100px;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    margin: 0px 0px 20px 0px;
}

.saved__list_item_imgcont img {
    display: block;
    max-width: 90px;
    max-height: 100px;
}

.saved__list_colors {
    background: #fff;
}

.saved__list_colors_element {
    display: flex;
    flex-direction: row;
    justify-content: flex-start;
    align-items: center;
    margin: 20px;
}

.saved__list_colors_picker {
    display: inline-block;
    width: 25px;
    height: 25px;
    margin: 5px;
    border: 1px solid #e1e1e1;
}

.saved__list_item_button {
    width: 100%;
    background-color: #800000;
    border: 0;
    outline: none;
    padding: 5px 0px 5px 0px;
    color: #fff;
    font-family: 'Open Sans',sans-serif;
    font-weight: 600;
    transition: all .3s ease-in;
}

.saved__list_item_button:hover {
    cursor: pointer;
    background: #fff;
    color: #800;
}

.saved__list_download {
    position: absolute;
    top: 10px;
    right: 10px;
}

.saved__list_download:hover {
    cursor: pointer;
}
</style>