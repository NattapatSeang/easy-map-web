<template>
    <div id='table'>
        <table class="table table-striped table-bordered" style="width: 100%;">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Detail</th>
                    <th>Access</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="map in mapArray" :key="map.id">
                    <td>{{map.name}}</td>
                    <td>{{map.description}}</td>
                    <td><button class="buttonEdt" v-on:click="edit(map.name)">edit</button></td>
                </tr>
            </tbody>
        </table>
    </div>
</template>


<script>
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios,axios)

export default {
    name: "map_list",
    data()
    {
        return {
            mapArray: undefined
        }
    },
    mounted()
    {
        Vue.axios.get('https://ap-southeast-1.aws.webhooks.mongodb-realm.com/api/client/v2.0/app/easy_map-aakbp/service/easy_map_map_api/incoming_webhook/map-get?secret=IAmDeveloper&command=show_all')
        .then((resp) => {
            //console.log(Object.keys(resp.data).length)
            var size = Object.keys(resp.data).length;
            this.mapArray = []

            for(var i = 0; i < size; i++)
            {
                //console.log(String.valueOf(resp.data[i]._id));
                //console.log(resp.data[i]._id.toString())
                //console.log(parseInt(resp.data[i].floor.$numberDouble).toString())

                const new_item = {
                    name: resp.data[i].name,
                    id: resp.data[i]._id.$oid,
                    floor: parseInt(resp.data[i].floor.$numberDouble),
                    description: resp.data[i].description,
                    creator_id: resp.data[i].creator_id,
                }
                if(new_item.floor == 1)
                {
                    this.mapArray.push(new_item)
                }
            }

            //console.log(resp.data)
            //console.log(resp.data[0].name)



        })
    }, methods: {
        edit(map_name)
        {
            var link = '/map_create?map_name=' + map_name

            this.$router.push(link); 
        }
    }
}

</script>

<style>
    #table {
        font-family: Arial, Helvetica, sans-serif;
        border-collapse: collapse;
        width: 100%;
    }

    #table td, #table th {
        border: 1px solid #ddd;
        padding: 8px;
    }

    #table tr:nth-child(even){background-color: #f2f2f2;}

    #table tr:hover {background-color: #ddd;}

    #table th {
        padding-top: 12px;
        padding-bottom: 12px;
        text-align: left;
        background-color: rgb(49, 17, 122);
        color: white;
    }

    .buttonEdt {
        background-color: #3c037e; /* Green */
        border: none;
        color: white;
        width: 100%;
        padding: 10px 0px;
        text-align: center;
        text-decoration: none;
        display: inline-block;
        font-size: 16px;
        transition-duration: 0.4s;
    }

    .buttonEdt:hover {
        background-color: #7524d1; /* Green */
        color: white;
    }
</style>