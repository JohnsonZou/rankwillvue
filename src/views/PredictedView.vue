<template>
  <div class="predicted">
    <h1>{{contestname}}</h1>
    <el-button type="primary" icon="el-icon-refresh-left" v-on:click="goback" circle style="margin-right: 1vw;"></el-button>
    <el-input v-model="input" placeholder="contestant name" @keyup.enter.native="srh" style="width: 15vw;"></el-input>
    <template style="width: 100vw;" v-if="nosrh">
      <el-table
      :data="contestant"
      style="width: width: 100vw">
      <el-table-column
        prop="rank"
        label="RANK"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="username"
        label="USERNAME"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="dataregion"
        label="REGION"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="oldrating"
        label="OLD RATING"
        align="center"
        style="width: 20vw;">
      </el-table-column>
      <el-table-column
        prop="deltarating"
        label="DELTA"
        align="center"
        style="width: 20vw;">
      </el-table-column>
      <el-table-column
        prop="newrating"
        label="NEW RATING"
        align="center"
        style="width: 20vw;">
      </el-table-column>

    </el-table>



  </template>

  <template style="width: 100vw;" v-if="yessrh">
      <el-table
      :data="srhresult"
      style="width: width: 100vw">
      <el-table-column
        prop="rank"
        label="RANK"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="username"
        label="USERNAME"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="dataregion"
        label="REGION"
        align="center"
        style="width: 15vw;">
      </el-table-column>
      <el-table-column
        prop="oldrating"
        label="OLD RATING"
        align="center"
        style="width: 20vw;">
      </el-table-column>
      <el-table-column
        prop="deltarating"
        label="DELTA"
        align="center"
        style="width: 20vw;">
      </el-table-column>
      <el-table-column
        prop="newrating"
        label="NEW RATING"
        align="center"
        style="width: 20vw;">
      </el-table-column>

    </el-table>


  </template>
  </div>
</template>

<script>
export default {
  name: 'PredictedView',
  data(){
    return{
      contestname:"",
      pageNum:1,
      pageSize:25,
      nosrh:true,
      yessrh:false,
      totalContestant:0,
      contestant:[],
      srhresult:[],
      input:"",
    }
  },
  components: {
    
  },
  created(){
    this.contestname=this.$route.params.contestname
    this.showpage()
  },
  methods:{
    showpage(){
      let formdata=new FormData();
      formdata.append("page",this.pageNum)
      formdata.append("contestname",this.contestname)
      fetch("http://localhost:9090/api/querypage",
        {
          method:"POST",
          body:formdata
        }
      ).then(res=>res.json()).then(res=>{
        this.totalContestant=res.data.contestantnum
        this.pageTotNum=Math.ceil(this.totalContestant/this.pageSize)
        this.contestant=res.data.result
        for(var i=0;i<this.contestant.length;i++){
          this.contestant[i].oldrating=parseFloat(this.contestant[i].oldrating).toFixed(2)
          this.contestant[i].newrating=parseFloat(this.contestant[i].newrating).toFixed(2)
          this.contestant[i].deltarating=parseFloat(this.contestant[i].deltarating).toFixed(2)
        }

      })
    },
    handleCurrentChange(){
      this.showpage()
    },
    srh(){
      this.srhresult=[]
      let formdata=new FormData();
      formdata.append("contestname",this.contestname)
      formdata.append("contestantname",this.input)
      fetch("http://localhost:9090/api/querybyname",
        {
          method:"POST",
          body:formdata
        }
      ).then(res=>res.json()).then(res=>{
        this.srhresult.push(res.data.result)
      })
      this.nosrh=false
      this.yessrh=true

    },
    goback(){
      this.srhresult=[]
      this.nosrh=true
      this.input=""
      this.yessrh=false
      this.pageNum=1
      this.showpage()
    }
  }
}
</script>