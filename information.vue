<template>
        <Layout :style="{margin:'10px',width:'1300px',height:'800px'}">
            <Button type="info" @click="divide">查看本地违规车辆信息</Button>
            <br>
            <h3>本地违规车辆信息</h3>
            <Table border :columns="columns2" :data="data2"></Table>
            <p></p>
        </Layout>   
</template>
<script>
import GLOBAL from '../api/global.js'
// console.log("id"+ GLOBAL.videoId)
import {getIllegalData} from '../api/api.js'
    var data1 = []
    var num = []//储存符合条件的id
    var data2 = []//储存符合条件的车辆信息
    export default {
        methods:{
                idJudge(){
                    if(this.temp!=GLOBAL.videoId){
                        this.loaddata()
                        this.temp = GLOBAL.videoId
                        // console.log(this.temp)
                    }else{
                        this.temp = GLOBAL.videoId
                    }
                },
                loaddata(){
                    getIllegalData(this.plate,GLOBAL.videoId,this.date,this.illegalMassage,this.area).then(response =>{
                        this.data1 = response.data
                        //console.log(GLOBAL.videoId)
                        //console.log(this.date)
                    })
                    //console.log(this.data1)
                    var plate
                    var temp
                    var t
                    var p = []
                    var area = []//储存车牌第一位字符
                    for(var i=0;i<this.data1.length;i++)
                    {
                      temp = JSON.parse(JSON.stringify(this.data1))[i]
                      plate = 'plate'
                      // console.log('plate')
                      p[i] = temp[''+plate+'']
                      //console.log(p[i])
                      t = p[i].toString()
                      area[i] = t.substring(0,1)
                      if(area[i]=="粤")
                        num[i] = i+1
                      //console.log(id[i])
                    }
                    this.divide()
                },
                
                divide(){
                  //console.log(id)     
                  var j=0
                  for(var i=0;i<num.length;i++)
                  {
                     var t = JSON.parse(JSON.stringify(this.data1))[i]
                     var id = 'id'
                     var id2 = t[''+id+'']
                     if(id2==num[i]){
                        data2[j] = this.data1[id2-1]
                        j++
                     }
                  }
                  console.log(data2)
                },
        },
        created: function () {
            this.loaddata()
        },
        mounted() {
                this.$nextTick(() => {
                    setInterval(this.idJudge, 500);
                })
        },
        data () {  
            return {
                temp:'',
                date:'', // *  日期数据
                plate:'',  // * 搜索输入框数据
                illegalMassage: '', // * 违规行为
                columns2: [
                    {
                        title: 'ID',
                        key: 'id'
                    },
                    {
                        title: '视频ID',
                        key: 'videoId'
                    },
                    {
                        title: '车牌号',
                        key: 'plate'
                    },
                    {
                        title: '上传时间',  
                        key: 'upload_time'
                    },
                    {
                        title: '违规时间',
                        key: 'illegal_time'
                    },
                    {
                        title: '违规行为',
                        key: 'illegal'
                    },
                    {
                        title: '违法图片',
                        key: 'img',
                        render: (h, params) => {
                            return h('viewer', [
                                h('img', {
                                    attrs: {
                                        src: params.row.img,
                                        style: 'width: 40px;height: 40px;'
                                    }
                                })
                            ])
                        },
                    },
                    {
                        title: '操作',
                        key: 'action',
                        render: (h,params) => {
                            return(
                                <div>
                                    <i-button type="error"   size="small" onClick={()=>{this.delete(params.index)}}>删除</i-button>
                                </div>
                            );
                        },
                    }
                ], 
                data2: data2,              
            }
        }
    }
</script>