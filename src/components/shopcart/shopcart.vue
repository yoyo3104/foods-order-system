<template>
<div>
    <div class="shopcart">
    <div class="content" @click="toggleList">
        <div class="content-left">
        <div class="logo-wrapper">
        <div class="logo" :class="{'highlight':totalCount>0}">
        <i class="icon-shopping_cart" :class="{'highlight':totalCount>0}"></i>
        </div>
        <div class="number" v-show="totalCount>0">{{totalCount}}</div>
        </div> 
       
        <div class="price-text" :class="{'highlight':totalPrice>0}">{{totalPrice}}元</div>
        <div class="desc-text">￥{{minprice}}元起送</div>
        
        
        
        
        </div>
        
        
        
        <div class="content-right">
        <div class="pay" :class="payClass">
        
        
        {{payDesc}}
        </div>


        
        </div>
        </div>
        <div class=ball-container>
        <div v-for="(ball,index) in balls">
        <transition name="drop"
                          v-on:before-enter="beforeEnter"
                          v-on:enter="enter"
                          v-on:after-enter="afterEnter">
        <div v-show="ball.show"  :key="index"  class="ball">
        <div class="inner inner-hook"></div>
        </div>
        </transition>
        </div>
        
        </div>
        <transition name="fold">
         
        <div class="shopcart-list" v-show="ListShow">
        <div class="list-header">
        <h1 class="title">购物车</h1>
        <span class="empty" @click="empty">清空</span>
        </div>
        <div class="list-content"  ref="listContent">
        <ul>
        <li class="food" v-for="food in selectFoods">
        <span class="name">{{food.name}}</span>
        <div class="price">
        
        <span>￥{{food.price*food.count}}</span>
        
        </div>
        <div class="cartcontrol-wrapper">
        
        <cartcontral :food="food" ></cartcontral>
        
        
        </div>
        </li>
        
        
        
        
        </ul>
        
        
        
        </div>
        
        
        
        </div>
        
    
    </transition> 
  
    
    </div>
     <transition name="fade">
      <div class="list-mask"  v-show="ListShow"></div>
    </transition>
    </div>
</template>

<script>
import BScroll from 'better-scroll';
import cartcontral from '../cartcontral/cartcontral';
   export default{
       props:{
           selectFoods:{
               type:Array,
               default() {
                   return [];
                  
                      }

           },
           deverprice:{
               type:Number,
               default:0,
           },
           minprice:{
               type:Number,
               default:0,
           },

       },
       data() {
          return{
              balls:[{
                   show:false
               },
                {
                   show:false
               },
                {
                   show:false
               },
                {
                   show:false
               },
                {
                   show:false
               }],
               dropBall:[],
               fold:true,


          };

       },
       computed:{
           totalPrice(){
               let total =0;
               this.selectFoods.forEach((food)=>{
                   total+=food.price*food.count;
               })
               return total;

               
           },
           totalCount(){
               let count =0;
               this.selectFoods.forEach((food)=>{
                   count+=food.count;
               })
               return count;
           },
            payDesc(){
               if(this.totalPrice===0){
               return `￥${this.minprice}元起配送`;
               }else if(this.totalPrice<this.minprice){
                let diff= this.minprice - this.totalPrice
                 return `还差￥${diff}元起送`;
               }else{
                   return `去结算`;
               }
           },
           payClass(){
               if(this.totalPrice<this.minprice){
                   return 'not-enough';
               }else{
                   return 'enough';
               }
           },
           ListShow(){
               if(!this.totalCount){
                   this.fold=true;
                   return false;
               }
               let show=!this.fold;
               if(show)
               {
                   this.$nextTick(()=>{
                       if(!this.scroll){
                       this.scroll=new BScroll(this.$refs.listContent,{
                           click:true
                       });
                       }else{
                           this.scroll.refresh();

                       }
                   })

               }
               return show;

           },
       
        
     } ,
     methods:{
         drop(el){
             for (let i=0; i<this.balls.length;i++){
                 let ball=this.balls[i];
                 if(!ball.show){
                     ball.show=true;
                     ball.el=el;
                     this.dropBall.push(ball);
                     return;
                 }
             }
         },
          beforeEnter(el){
              let count=this.balls.length;
              while(count--){
                  let ball=this.balls[count]
                  if(ball.show){
                  let rect= ball.el.getBoundingClientRect();
                  let x =rect.left -32;
                  let y =-(window.innerHeight-rect.top-22);
                  el.style.display='';
                  el.style.webkitTransform = `translate3d(0,${y}px,0)`;
                  el.style.transform = `translate3d(0,${y}px,0)`;
                  let inner=el.getElementsByClassName('inner-hook')[0];//用来对js选择的
                  inner.style.webkitTransform = `translate3d(${x}px, 0, 0)`;
                  inner.style.transform = `translate3d(${x}px, 0, 0)`;
                  }




              }

          },
          enter(el) {
              
                /* 触发浏览器重绘，重绘之后才可以设置transform*/
              let rf=el.offsetHeight;
              this.$nextTick(() => { //样式重置                    
              el.style.webKitTransform = 'translate3d(0,0,0)';//没有变量时只能用单引，不能用反引                  
                el.style.transform = 'translate3d(0,0,0)';                    
                let inner = el.getElementsByClassName('inner-hook')[0];                   
                 inner.style.webkitTransform = 'translate3d(0,0,0)';                    
                 inner.style.transform = 'translate3d(0,0,0)';              
                   });




          },
          afterEnter(el){
              let ball =this.balls.shift();
              if(ball){
                  ball.show=false;
                  el.style.display='none';
              }


          },
          toggleList(){
              if(!this.totalCount){
                  return;
              }
              this.fold=!this.fold;

          },
          empty(){
              this.selectFoods.forEach((food)=>{
                  food.count=0;
              })

          }
     },
     components:{
           
           cartcontral,

       },
   }
</script>

<style lang="stylus" rel="stylesheet/stylus" >
@import "../../common//stylus/mixin.styl"
.shopcart
    position:fixed
    left:0
    bottom:0
    width:100%
    height:48px
    z-index:50
    background:#141d27
    .content
        display:flex
        font-size:0
        .content-left
            flex:1
            .logo-wrapper
                display:inline-block
                position:relative
                top:-10px
                margin:0 12px;
                padding:6px
                width:56px
                height:56px
                box-sizing:border-box
                border-radius:50%
                vertical-align:top
                background:#141d27
                .logo
                    width:100%
                    height:100%
                    border-radius:50%
                    background:#2b343c
                    margin-bottom:8px
                    text-align:center
                    &.highlight
                        background:rgb(0,160,220)
                    .icon-shopping_cart
                        color:rgba(255,255,255,0.4)
                        line-height:44px
                        font-size:24px
                        border-radius:50%
                        &.highlight
                            color:#fff
                .number
                    position:absolute
                    top:0 
                    right:0
                    width:24px
                    height:16px
                    line-height:16px
                    text-align:center
                    border-radius:16px
                    font-size:9px
                    font-weight:400
                    color:#fff
                    background:rgb(240,20,20)
                    box-shadow:0 4px 8px 0 rgba(0,0,0,0.4)


            .price-text
                display:inline-block
                vertical-align: top
                margin-top: 12px
                line-height: 24px
                padding-right: 12px
                box-sizing: border-box
                border-right: 1px solid rgba(255, 255, 255, 0.1)
                font-size: 16px
                font-weight: 700
                color:rgba(255, 255, 255, 0.1)
                &.highlight
                    color:#fff
            .desc-text
                display: inline-block
                vertical-align: top
                margin: 12px 0 0 12px
                line-height: 24px
                font-size: 10px
                color:rgba(255, 255, 255, 0.1)
            



                    


        .content-right
            flex:0 0 105px
            width:105px
            height:52px
            
            background:rgba(0,0,0,0.2)
            .pay
                height:48px
                line-height:48px
                text-align:center
                font-size:12px
                font-weight:700
                color:rgba(255, 255, 255, 0.1)
                &.not-enough
                    background: #2b333b
                &.enough
                    color:#fff
                    background: #004b3c
        
    .ball-container
        .ball
            position:fixed
            left:32px
            bottom:22px
            z-index:200
            &.drop-enter-active, &.drop-leave-active
                transition: all 1s cubic-bezier(0.49, -0.29, 0.75, 0.41)
                .inner
                    width:16px
                    height:16px
                    border-radius:50%
                    background:rgb(0,160,220)
                    transition:all 1s linear
    .shopcart-list
        position:absolute
        left:0
        top:0
        z-index:-1
        width:100%
        transform: translate3d(0, -100%, 0)
        &.fold-enter-active, &.fold-leave-active
          transition: all 0.5s
        &.fold-enter, &.fold-leave-active
          transform: translate3d(0, 0, 0)
        .list-header
            height:40px
            line-height:40px
            padding:0 18px
            background: #f3f5f7
            border:1px solid rgba(7.17.27.0.1)
            .title
                float:left
                font-size:14px
                color:rgb(7,17,27)
            .empty
                float:right
                font-size:12px
                color:rgb(0,160,220)
        .list-content
            padding:0 18px
            max-height:217px
            overflow:hidden
            background:#fff
            .food
                position:relative
                padding:12px 0
                box-sizing:border-box
                border-1px(rgba(7,17,27,0.1))
                .name
                    line-height:24px
                    font-size:14px
                    color:rgb(7,17,27)
                .price
                    position:absolute
                    right:90px
                    bottom:12px
                    line-height:24px
                    font-size:14px
                    color:rgb(240,20,20)
                    font-weight:700
                .cartcontrol-wrapper
                    position:absolute
                    right:0
                    bottom:6px

.list-mask
    position:fixed
    width:100%
    height:100%
    top:0
    left:0
    z-index:40
    backdrop-filter: blur(10px)
    background: rgba(7, 17, 27, 0.8)
    &.fade-enter-active, &.fade-leave-active
      transition: all 0.5s
    &.fade-enter, &.fade-leave
      opacity: 0
      background: rgba(7, 17, 27, 0)


















</style>