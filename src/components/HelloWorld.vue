<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <input v-model="newItem" @keyup.enter="addNew">
    <ul>
<li v-for="item in items" v-bind:class="{finished:item.isFinished}" v-on:click="toggleFinish(item)">
    {{item.label}}
</li>
 

    </ul>
    
  </div>
</template>

<script>
import Store from './storage'
console.log(Store)
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'todo-list练习！',

      items:Store.fetch(),
   newItem:"",


  }
  },
watch:{
  items:{
  handler:function(items){
  Store.save(items)

  },
  deep:true
  }
},


  methods:{
    toggleFinish:function(item){
    console.log(item.isFinished =!item.isFinished );
 },

    addNew:function(){
     this.items.push({
     label:this.newItem,
      isFinished:false
    })
      this.newItem="";
      Store.save()

    },

 }

  }






</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.finished{
  color:red;
}
h1, h2 {
  font-weight: normal;
}
li{
  list-style:none;
}
a {
  color: #42b983;
}
</style>
