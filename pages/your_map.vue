<template>
    <div id="app">
        <h1>Map List</h1>
        <map_list/>
        <form
            accept-charset="UTF-8"
            v-on:submit.prevent="createMap"
            method="POST"
        >
            <div>
                <label>Map Name</label>
                <input
                    type="text"
                    v-model="name"
                    class="form-control"
                    placeholder="Name"
                >
            </div>
            <div>
                <label>Description</label>
                <input
                    type="textarea"
                    v-model="description"
                    class="form-control"
                    placeholder="Description"
                >
            </div>
            <button type="submit" class="button">add</button>
        </form>
    </div>
</template>
<script>
import map_list from './components/map_list.vue'
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios,axios)

var creator_id = "60b5dc380c41fa91b1f363fd"
var floor = 1

export default {
    name: 'App',
    components: {
        map_list
    },
    data() {
        return {
            creator_id:"",
            name:"",
            floor: "",
            description: ""
        }
    },
    methods: {
        createMap()
        {
            Vue.axios.post("https://ap-southeast-1.aws.webhooks.mongodb-realm.com/api/client/"
            + "v2.0/app/easy_map-aakbp/service/easy_map_map_api/incoming_webhook/map-post?secret=IAmDeveloper"
            + "&creator_id=" + creator_id
            + "&name=" + this.name
            + "&floor=" + floor
            + "&description=" + this.description)

            var link = '/map_create?map_name=' + this.name

            this.$router.push(link); 
        }
    }
}
</script>
<style>
    #app {
        font-family: Arial, Helvetica, sans-serif;
        color: indigo;
        margin-top: 60px;
        text-align: center;
        margin-left: auto;
        margin-right: auto;
    }

    .button {
        background-color: #3c037e; /* Green */
        border: none;
        color: white;
        padding: 10px 0px;
        text-align: center;
        width: 100px;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        transition-duration: 0.4s;
    }

    .button:hover {
        background-color: #7524d1; /* Green */
        color: white;
    }

    input[type=text] {
        width: 20%;
        padding: 12px 20px;
        margin: 8px 0;
        display: inline-block;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }

    input[type=textarea] {
        width: 20%;
        height: 200px;
        padding: 12px 20px;
        margin: 8px 0;
        display: inline-block;
        border: 1px solid #ccc;
        border-radius: 4px;
        box-sizing: border-box;
    }
</style>