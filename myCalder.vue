<template>
<div  id="mycalender">
<div class="header">
   <div><img src="../assets/left.png"  @click="goPre" v-show="goPreFlag">  <span class="title" @click="showContent">{{getText}}</span> <img src="../assets/right.png"   @click="goNext" v-show="goNextFlag"></div> 
</div>
<div class="main">
    
    <!-- 年内容 -->
    <div class="year" v-show='currentLevel==1'>
        <span v-for="item in  this.dataList"  @click='chooseYear(item)' :class="{'clickadd':year==item.year}">{{item.year}}</span>
    </div>
    <!-- 月内容 -->
    <div class="month" v-show='currentLevel==2'>
      <span v-for="item in 12"   @click='chooseMonth(item)' :class="{'clickadd':month==item}">{{item}}月</span>
    </div>
 <!-- 天内容 -->
    <div class="date"  v-show='currentLevel==3'>     
       <!-- 星期 -->
        <div class="week">
                <span>星期日</span> <span>星期一</span> <span>星期二</span> <span>星期三</span> <span>星期四</span> <span>星期五</span> <span>星期六</span> 
       </div>
        <div class="day">
            <span v-for="item in getMatchFirstDate(this.year,this.month)"  class="none"></span>
            <span v-for=" day  in  this.dayList"  @click="chooseDate(day)"  :class="{'clickadd':date==day.date}">{{day.date}}</span> 
         </div>   
    </div>
</div>
</div>
</template>

<script>
export default {
    data(){
        return {
            //  默认当前是第一层年份 2月  3日
           currentLevel:1,
           year:'',
           month:'',
           endYear:'',
           startYear:'',
           date:'',
           goNextFlag:true,
           goPreFlag:true,
           dayList:[],//  存放具体每月天数的数据
           dataList :[],//  日历的数据
        }
    },
    mounted(){
        // this.getCalenderData();
        this.getMonth();
    },
    computed: {
        //日历头部显示
      getText(){
        //   if(this.currentLevel==3){
        //       return `${this.year}年${this.month}月${this.date}日`
        //   }else if(this.currentLevel==2 ) {
        //      return `${this.year}年${this.month}月`
        //   }else if(this.currentLevel==1){
        //         return `${this.year}年`
        //   }

         if(this.year &&  this.month  &&  this.date){
              return `${this.year}年${this.month}月${this.date}日`
          }else if(this.year &&  this.month  ) {
             return `${this.year}年${this.month}月`
          }else if(this.year ){
                return `${this.year}年`
          }
      } ,
    //   获取当前位置  
    },
    methods:{
        // 左右按钮
        goPre(){
            console.log(this.currentLevel,'thisssssss')
        //  如果当前是切年
            if(this.currentLevel==1){
                this.year-=1;
                if(this.year<this.endYear){
                    this.goNextFlag=  true;
                }
                if(this.year==this.startYear){
                    // 如果超过预定5年 就隐藏右边按钮
                    this.goPreFlag=  false;
                } 
            }
             if(this.currentLevel==2||this.currentLevel==3){
                this.month-=1;
                if(this.month==0){
                    this.year-=1;
                    this.month=12;
                     if(this.year<this.endYear){
                    this.goNextFlag=  true;
                   }
                    if(this.year<=this.startYear){
                        this.year=this.startYear;
                        this.goPreFlag=  false;
                    }
                }
            }
        },
        goNext(){
            //  如果当前是切年
            console.log(this.currentLevel,'thisssssss')
            if(this.currentLevel==1){
                this.year+=1;
                if(this.year>this.startYear){
                    this.goPreFlag=true;
                }
                if(this.year==this.endYear){
                    // 如果超过预定5年 就隐藏右边按钮
                    this.goNextFlag=  false;
                }       
            }
            if(this.currentLevel==2 ||this.currentLevel==3){
                this.month+=1;
                if(this.month==13){
                    this.year+=1;
                    this.month=1;
                if(this.year>this.startYear){
                    this.goPreFlag=true;
                }
                    if(this.year>=this.endYear){
                        this.year=this.endYear;
                        this.goNextFlag=  false;                        
                    }
                }                 
            }
        },
        //选择年份
        chooseYear(val){
           console.log(val.year,'yeaeeeeeeeee');
           this.year=val.year;
           this.month='';
           this.date='';
           this.$emit('chooseYear',val);  
           this.currentLevel=2;
           console.log(this.currentLevel,'thisssssss')
        },
        chooseMonth(month){
            console.log(month,'yeaeeeeeeeee');
            this.month= month;
            this.date='';
             this.currentLevel=3
              this.$emit('chooseMonth',month);
              this.searchDate(this.year,this.month)
            console.log(this.currentLevel,'thisssssss')
                    },
        chooseDate(item){
            this.date= item.date;
            this.$emit('chooseDate',item);
            console.log(this.currentLevel,'thisssssss')
        },
        // 计算当前月的第一天是星期几
         getMatchFirstDate(year,month){
        return new Date(year,month-1,1).getDay();
      },
        showContent(){
             this.currentLevel-=1;
             if(this.currentLevel<=0){
             this.currentLevel=1;
             this.date='';
             this.month='';
                 return ;
             } else if(this.currentLevel==1){
                //  this.month='';
                 this.date='';
             }           
                    },
                    // 根据年份&& 月份查询天
       searchDate(year,month){
        let filteryear= this.dataList.find(yearobj=> yearobj.year==year);
        console.log(filteryear,'filteryear>');
        let filtermonth = filteryear.children.find(monthobj=>monthobj.month==month);
        console.log(filtermonth,'filtermonth>');
        this.dayList =filtermonth.children;
       },
                    // 查询每年固定月份的天数  
                    //判断是不是闰年
                    // 闰年对应的12个月份的天数[31,29,31,30,31,30,31,31,30,31,30,31]
                    //  平年对应的月份天数[31,28,31,30,31,30,31,31,30,31,30,31]
       isRunYear(year){
             if( year%400==0||year %100!==0 &&year %4==0){
                return true
                        }
                        return false
                    },
         // 生成天数数组
             getdate(month,a){
               let list=[]
                for(let i=1;i <= a[month-1] ;i++){
                            const obj ={
                                date:i,
                            }
                            list.push(obj);
                        }
                        return list;
                    },                
                    // 生成月份
                    getMonth(year){
                        let tempA=[];
                        let month=12;
                        let currentYear =  new Date().getFullYear();
                        let endYear  =  currentYear+5;
                        this.endYear=endYear;
                        let startYear = currentYear -5;
                        this.startYear = startYear;
                    console.log(currentYear,startYear,endYear);
                    for(let i= startYear; i<=endYear;i++){  
                        const obj ={
                            year:i,
                            children:[]
                        };
                        this.dataList.push(obj);
                    }
                    //  生成月份
                    this.dataList.forEach(item=>{
                    for(let month= 1;month<=12;month++){
                        item.children.push({month:month,children:[]})
                    };
                    // 生成天数
                    if(this.isRunYear(item.year)){
                        tempA= [31,29,31,30,31,30,31,31,30,31,30,31];
                    }else {
                        tempA= [31,28,31,30,31,30,31,31,30,31,30,31];
                    }
                    item.children.forEach(itemChildren=>{
                    let list=  this.getdate(itemChildren.month,tempA);
                    itemChildren.children.push(...list);               
                    })
                    })
                        console.log( this.dataList,' this.dataList>>>>>>')
                    },

                }
            }
</script>


<style lang="less">
#mycalender {
    width:100%;
    height: 300px;
    // background-color: pink;
    .header {
        div {
            height: 30px;
            padding: 10px;
            margin :0 auto;
            line-height: 30px;
            img {
              width:15px;
              height:15px;
            }
            .title  {
              font-size:18px;
              display: inline-block;
              padding : 0 20px;
            }
            
        }
    }
    .main{
        .month,
      .year ,
      .day
      {
        //   display: flex;
        .none{
            border:none;           
        }
          span{
            // width:30%/
            display:inline-block;
            height:100px;
            width:14.2%;
            line-height: 100px;
            text-align: center;
            border:1px solid #ccc;
          }
          span:active {
              border-radius:8px 8px 8px 8px;
              background-color: lightseagreen;
          }
      }
      .clickadd{
            background-color: lightseagreen;
            border-radius:8px 8px 8px 8px;
      }
      .week{
          display:flex;
          background-color: pink;
          span{
              height:40px;
              text-align: center;
              line-height: 40px;
              flex:1
          }
      }
    }
}
</style>
