<template>
  <div class="home" style="justify-content: center;width: 100vw;text-align: center;">
    <template style="width: 90vw;">
    <el-table
      :data="contest"
      style="width: width: 90vw">
      <el-table-column
        prop="contestName"
        label="CONTEST"
        align="center"
        style="width: 45vw;">
        <template slot-scope="props">
          <el-link type="primary" :href="'predicted/'+props.row.contestName"  :underline="false">{{props.row.contestName}}
            
          </el-link>
          </template>
      </el-table-column>
      <el-table-column
        prop="updateTime"
        align="center"
        label="UPDATE TIME"
        style="width: 45vw;">
      </el-table-column>
    </el-table>

    <el-pagination
      @current-change="handleCurrentChange"
      background
      layout="prev, pager, next"
      :pageSize="pageSize"
      :current-page.sync="pageNum"
      :total="totalContest">
    </el-pagination>


  </template>
  </div>
</template>

<script>
export default {
  name: 'HomeView',
  data(){
    return{
      contest:[],
      totalContest:0,
      pageNum:1,
      pageSize:10,
      pageTotNum:0,
    }
  },
  created(){
    this.showpage()
  },
  methods:{
    add0(t){
      if(t<10)return '0'+t
      return t
    },
    format(t){
      var time=new Date(parseInt(t))
      var yy=time.getFullYear(),mm=time.getMonth()+1,dd=time.getDate()
      var hh=time.getHours(),min=time.getMinutes(),ss=time.getSeconds()
      return (yy+'-'+this.add0(mm)+'-'+this.add0(dd)+' '+
      this.add0(hh)+':'+this.add0(min)+':'+this.add0(ss))
    },
    showpage(){
      let formdata=new FormData();
      formdata.append("page",this.pageNum)
      fetch("http://localhost:9090/api/getcontestbypage",
        {
          method:"POST",
          body:formdata
        }
      ).then(res=>res.json()).then(res=>{
        this.contest=res.data.data
        this.totalContest=res.data.totnum
        this.pageTotNum=Math.ceil(this.totalContest/this.pageSize)
        for(var i=0;i<this.contest.length;i++){
          this.contest[i].updateTime=this.format(this.contest[i].updateTime*1000)
        }
      })
    },
    handleCurrentChange(){
      this.showpage()
    },
    handleClick(contestName){

    }
  }
}
</script>
