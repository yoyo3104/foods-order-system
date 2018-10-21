<template>
    <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper" >
    <ul>
        <li v-for="(good,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
            <span class="text border-1px">
            <span v-show="good.type>0" class="icon" :class="classMap[good.type]"></span>{{good.name}}
            
            </span>


        </li> 
    </ul> 
    
    </div>
    <div  class="goods-wrapper" ref="foodWrapper">
    <ul>
      <li v-for="item in goods" class="food-list food-list-hook">
        <h1 class="title">{{item.name}}</h1>
        <ul>
        <li v-for="food in item.foods" class="food-item border-1px">
        
        <div class="icon">
        <img width="57" height="57" :src="food.icon">
        </div>

        <div class="content">
         <h1 class="name">{{food.name}}</h1>
        <p class="desc">{{food.description}}</p>
        <div class="extra">
        <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
        
        </div>

        <div class="price">
        <span class="now">￥{{food.price}}</span>
        <span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
        <div class="contrl-wrapper">
        <cartcontral :food="food" v-on:cart-add="cartAdd"></cartcontral>
        
        </div>
        </div>
        

    
        </div>
 
     </li>
        
        
    </ul>
      
      
      
</li>
      
      
      
      
      
  </ul>  
    
    
    
    
    </div>
    <shopcart :deverprice="seller.deliveryPrice" :minprice="seller.minPrice" :selectFoods="selectFoods"  ref="shopcart"></shopcart>
    
    </div>
</template>

<script>
import BScroll from 'better-scroll';
import shopcart from '../shopcart/shopcart';
import cartcontral from '../cartcontral/cartcontral';
   export default{

       props:{
           seller:{
               type:Object
           }

       },
       data() {
           return {
               goods:[],
               listHeight:[],
               scrollY:0,
           };

       },
       computed:{
           currentIndex(){
               for(let i=0;i<this.listHeight.length;i++){
                   let height1=this.listHeight[i];
                   let height2=this.listHeight[i+1];
                   if(!height2||(this.scrollY>=height1&&this.scrollY<height2)){
                       return i;
                   }
                   


               }return 0;
               
           },
          selectFoods(){
               let foods=[];
               this.goods.forEach((good)=>{
                good.foods.forEach((food)=>{
                    if(food.count){
                        foods.push(food);
                    }
                   })

               })
                return foods;
           }
       },
       created() {
           this.classMap=['decrease','discount','special','invoice','guarantee'];
           this.$http.get('./api/goods').then((response)=>{
               response=response.body;
      
           const ERROK=0;
         if(response.errno === ERROK){
          this.goods=response.data;
          this.$nextTick(()=>{
              this._initScroll();
              this._calculateHeight();

          });
          console.log(this.goods);
         }

           });
       },
       methods:{
           _initScroll() {
           this.menuScroll=new BScroll(this.$refs.menuWrapper,{click: true});
           this.foodScroll=new BScroll(this.$refs.foodWrapper,{click: true, //取消默认阻止事件
          probeType: 3});
            //监听事件的触发时间，1为即时触发，3为延迟到事件完毕后触发});
            this.foodScroll.on('scroll',(pos)=>{
                this.scrollY=Math.abs(Math.round(pos.y));
            })



           },
           _calculateHeight(){
                let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
        let height = 0;
        this.listHeight.push(height);
        for (let i = 0; i < foodList.length; i++) {
          let item = foodList[i];
          height += item.clientHeight;
          this.listHeight.push(height);
        }
      
           },
           selectMenu(index,event){
               console.log(event);
               if(!event._constructed){
                   return;
               }
               let foodList = this.$refs.foodWrapper.getElementsByClassName('food-list-hook');
               let el=foodList[index];
               this.foodScroll.scrollToElement(el,300);

               

           },
           cartAdd(target){
                this.$nextTick(() => { //回调函数异步执行，两个动画效果就不会卡顿了
           this.$refs.shopcart.drop(target);
    });

           },

       },
       components:{
           shopcart,
           cartcontral,

       },
     } 
</script>

<style lang="stylus" rel="stylesheet/stylus">
@import "../../common/stylus/mixin.styl"
.goods
    position:absolute
    display:flex
    width:100%
    top:174px
    bottom:46px
    overflow:hidden
    .menu-wrapper
        flex:0 0 80px
        width:80px
        background: #f3f5f7
        .menu-item
            display:table
            height:54px
            width:56px
            line-height:14px
            padding:0 12px
            &.current
                position:relative
                z-index:10;
                line-height:28px
                font-size:24px
                font-weight:1000
                background:#fff
                color:rgb(240,20,20)

            .icon
                display: inline-block
                vertical-align: top
                width: 12px
                height: 12px
                margin-right: 2px
                background-size: 12px 12px
                background-repeat: no-repeat
                &.decrease
                  bg-image('decrease_3')
                &.discount
                  bg-image('discount_3')
                &.guarantee
                  bg-image('guarantee_3')
                &.invoice
                  bg-image('invoice_3')
                &.special
                  bg-image('special_3')
            .text
                display: table-cell
                width: 56px
                vertical-align: middle
                border-1px(rgba(7, 17, 27, 0.1))
                font-size: 12px


                 

    .goods-wrapper
        flex: 1
        .title
            padding-left: 14px
            height: 26px
            line-height: 26px
            border-left: 2px solid #d9dde1
            font-size: 12px
            color: rgb(147, 153, 159)
            background: #f3f5f7
        .food-item
            display:flex
            margin:18px
            padding-bottom:18px
            border-1px(rgba(7,17,27,0.1))
            &:last-child
                border-none()
                margin-bottom:0
            .icon
                flex:0 0 57px
                margin-right:10px
            .content
                flex:1
                .name
                    margin:2px 0 8px 0
                    height:14px
                    line-height:14px
                    font-size:14px
                    color:rgb(7,17,27)
                .desc, .extra
                    
                    line-height:10px
                    font-size:10px
                    color: rgb(147, 153, 159)
                .desc
                    line-height:12px
                    margin-bottom:8px
                .extra
                    .count
                        margin-right:12px

                .price
                    font-weight: 700
                    line-height:24px
                    .now
                        margin-right:8px
                        font-size:14px
                        color:rgb(240,20,20)
                    .old
                        text-decoration:line-through
                        font-size:10px
                        color:rgb(147, 153, 159)
                    .contrl-wrapper
                         position:absolute
                         right:0
                         bottom:12px



                








                

      


 
</style>