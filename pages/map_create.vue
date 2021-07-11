<template>
  <div class="con-center">
    <h1>Map: {{$route.query.map_name}}</h1>

    <svg width="500" height="500" xmlns="http://www.w3.org/2000/svg" style="border:1px solid #000000;">
      <g>
        <rect v-for="obj in objectArray" :key="obj.id" :x=obj.location_x :y=obj.location_y :width=obj.width_x :height=obj.width_y style="fill:blue;stroke:pink;stroke-width:5;opacity:0.5" />
      </g>
      Sorry, your browser does not support inline SVG.
    </svg>

    <table class="table table-striped table-bordered" style="width: 100%;">
      <thead>
        <tr>
          <th>Name</th>
          <th>Detail</th>
          <th>Loc X</th>
          <th>Loc Y</th>
          <th>Width X</th>
          <th>Width Y</th>
          <th>Edit</th>
          <th>Delete</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="obj in objectArray" :key="obj.id">
          <td>{{obj.name}}</td>
          <td>{{obj.detail}}</td>
          <td>{{obj.location_x}}</td>
          <td>{{obj.location_y}}</td>
          <td>{{obj.width_x}}</td>
          <td>{{obj.width_y}}</td>
          <td><button class="buttonEdt" v-on:click="saveObj(obj.id)">edit</button></td>
          <td><button class="buttonDel" v-on:click="delObj(obj.id)">delete</button></td>
        </tr>
      </tbody>
    </table>

    <form
      accept-charset="UTF-8"
      v-on:submit.prevent="createObj"
      method="POST"
    >
      <div>
        <label>Object Name</label>
        <input
          type="text"
          v-model="name"
          class="form-control"
          placeholder="Name"
        >
      </div>
      <div>
        <label>Detail</label>
        <input
          type="textarea"
          v-model="detail"
          class="form-control"
          placeholder="Detail"
        >
      </div>
      <div>
        <label>Location X</label>
        <input
          type="text"
          v-model="location_x"
          class="form-control"
          placeholder="Location X (integer)"
        >
      </div>
      <div>
        <label>Location Y</label>
        <input
          type="text"
          v-model="location_y"
          class="form-control"
          placeholder="Location Y (integer)"
        >
      </div>
      <div>
        <label>Width X</label>
        <input
          type="text"
          v-model="width_x"
          class="form-control"
          placeholder="Width X (integer)"
        >
      </div>
      <div>
        <label>Width Y</label>
        <input
          type="text"
          v-model="width_y"
          class="form-control"
          placeholder="Width Y (integer)"
        >
      </div>
      <button type="submit" class="buttonAdd">add</button>
    </form>
    <div height="100"></div>

  </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'
Vue.use(VueAxios,axios)

export default {
  data() {
    return {
      mapArray: undefined,
      objectArray: undefined,
      name: "",
      detail: "",
      location_x: "",
      location_y: "",
      width_x: "",
      width_y: "",
      currentFloor: ""
    }
  },
  mounted()
  {
    Vue.axios.get('https://ap-southeast-1.aws.webhooks.mongodb-realm.com/api/client/v2.0/app/easy_map-aakbp/service/easy_map_map_api/incoming_webhook/map-get?secret=IAmDeveloper&command=show_same_map&map_name=' + this.$route.query.map_name)
    .then((resp) => {
      //console.log(Object.keys(resp.data).length)
      var size = Object.keys(resp.data).length;
      var firstFloor = 0
      this.mapArray = []

      for(var i = 0; i < size; i++)
      {
        const new_item = {
          name: resp.data[i].name,
          id: resp.data[i]._id.$oid,
          floor: parseInt(resp.data[i].floor.$numberDouble),
          description: resp.data[i].description,
          creator_id: resp.data[i].creator_id,
        }

        if(new_item.floor == 1)
        {
          firstFloor = i
          this.currentFloor = firstFloor
        }

        this.mapArray.push(new_item)
      }

      Vue.axios.get('https://ap-southeast-1.aws.webhooks.mongodb-realm.com/api/client/v2.0/app/easy_map-aakbp/service/easy_map_mapobject_api/incoming_webhook/mapobject-get?secret=IAmDeveloper&command=show_all_in_map&in_map=' + this.mapArray[firstFloor].id)
      .then((respObj) => {
        var sizeObj = Object.keys(respObj.data).length;
        this.objectArray = []

        for(var i = 0; i < sizeObj; i++)
        {
          const new_item = {
            name: respObj.data[i].name,
            id: respObj.data[i]._id.$oid,
            map_id: respObj.data[i].map_id.$oid,
            location_x: parseInt(respObj.data[i].location_x.$numberDouble),
            location_y: parseInt(respObj.data[i].location_y.$numberDouble),
            width_x: parseInt(respObj.data[i].width_x.$numberDouble),
            width_y: parseInt(respObj.data[i].width_y.$numberDouble),
            detail: respObj.data[i].detail,
          }
          this.objectArray.push(new_item)
        }

        //alert(this.objectArray.length)
      })
    })
  },
  methods: {
        delObj(obj_id)
        {
            alert("will implement delete " + obj_id)
        },
        saveObj(obj_id)
        {
            alert("will implement save " + obj_id)
        },
        createObj()
        {
          Vue.axios.post("https://ap-southeast-1.aws.webhooks.mongodb-realm.com/api/client/v2.0/app/easy_map-aakbp/service/"
          + "easy_map_mapobject_api/incoming_webhook/mapobject-post?secret=IAmDeveloper"
          + "&name=" + this.name
          + "&map_id=" + this.mapArray[this.currentFloor].id
          + "&detail=" + this.detail
          + "&location_x=" + this.location_x
          + "&location_y=" + this.location_y
          + "&width_x=" + this.width_x
          + "&width_y=" + this.width_y)

          alert(this.name + " in " + this.mapArray[this.currentFloor].id)
          window.location.reload()
        }
    }
}
</script>

<style>
    .con-center {
        text-align: center;
        margin-top: 1rem;

  
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

    .buttonDel {
        background-color: #a10000; /* Green */
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

    .buttonDel:hover {
        background-color: #dd3232; /* Green */
        color: white;
    }

    .buttonAdd {
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
        margin-top: 25px;
        margin-bottom: 25px;
    }

    .buttonAdd:hover {
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