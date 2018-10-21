<template>
   <div class="cartcontral" >
   <transition>
    <div class="cart-decrease " v-show="food.count>0" @click="decreaseCart" >
    <span class="inner icon-remove_circle_outline "></span>
    
    </div>
    </transition>
    <div class="cart-count" v-show="food.count>0">{{food.count}}</div>
    <div class="cart-add icon-add_circle" @click="addCart"></div>
   
   
   </div>
</template>

<script>
import VUE from 'vue'
   export default{
       props:{
           food:{
               type:Object,
           }
       },
       created() {
           console.log(this.food)
       },
       methods:{
           addCart(event){
               if(!event._constructed){
                   return;
               }
              
               if(!this.food.count){
                   VUE.set(this.food,'count',1);
               }else{
                   this.food.count++;
               }
               this.$emit('cart-add', event.target);

           },
           decreaseCart(event){
               if(!event._constructed){
                   return;
               }
              
               if(this.food.count){
                   this.food.count--;
               
           }
       },
       }
   } 
</script>


<style  lang="stylus" rel="stylesheet/stylus">
.cartcontral
    font-size:0
    .cart-decrease
        display:inline-block
        padding:6px
        .inner
            display:inline-block
            line-height:24px
            font-size:24px
            color:rgb(0,130,220)
            transition: all 0.4s linear
          &.v-enter-active, &.v-leave-active
            transition: all 0.4s linear
          &.v-enter, &.v-leave-active   //刚进入和离开后的状态
            opacity: 0
            transform: translateX(24px)
            .inner1
             transform: rotate(180deg)
           

    .cart-count
        display:inline-block
        vertical-align:top
        width:12px
        padding:6px
        line-height:24px
        text-align:center
        font-size:10px
        color:rgb(147,153,159)


    .cart-add
        display:inline-block
        padding:6px
        line-height:24px
        font-size:24px
        color:rgb(0,130,220)


    
</style>