
<template>



  <div id="app">
    <el-dialog title="SIGN IN/SIGN UP" :visible.sync="authFormVisible">
      <el-form :model="form">
        <el-form-item label="Email">
          <el-input v-model="form.email" autocomplete="off" placeholder="your email"></el-input>
        </el-form-item>
        <el-form-item label="Password">
          <el-input v-model="form.password" show-password autocomplete="off" placeholder="no less than 6 letter"></el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="signin">SIGNIN</el-button>
        <el-button type="primary" @click="signup">SIGNUP</el-button>
      </div>
    </el-dialog>


    <el-dialog title="FOLLOW LIST" :visible.sync="followTableVisible">
      <el-table :data="followings">
        <el-table-column property="date" label="LEETCODE USERNAME" width="150"></el-table-column>
      </el-table>
    </el-dialog>



    <el-menu  class="el-menu-demo" mode="horizontal" >
      <el-menu-item v-on:click="authFormVisible=true" v-show="!loginstatus">SIGN IN / SIGN UP</el-menu-item>
      <el-submenu v-show="loginstatus" index="2">
        <template slot="title">{{email}}</template>
        <el-menu-item index="2-1" v-on:click="followTableVisible = true">FOLLOWING</el-menu-item>
        <el-menu-item index="2-2" v-on:click="logout">LOG OUT</el-menu-item>
      </el-submenu>

      <!-- <el-menu-item v-on:click="authFormVisible=true">SIGN IN / SIGN UP</el-menu-item> -->
    </el-menu>
    <nav>
      <router-link to="/">Home</router-link> 
    </nav>
    <router-view/>
  </div>
</template>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

nav {
  padding: 30px;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}
</style>
<script>

import { sha256 } from 'js-sha256'

export default {
  data(){
    return{
      authFormVisible:false,
      followTableVisible:false,
      form:{
        email:'',
        password:'',
      },
      loginstatus:false,
      email:'',
      token:'',
    }
  },
  
  
  created(){
    this.form.email=""
    this.form.password=""
    let curToken=localStorage.getItem('token')
    this.validToken(curToken)
  },
  methods:{
    getFollowList(){

    },
    validToken(token){
      if(token===null)return
      fetch("http://localhost:9090/api/auth/info",
        {
          method:"GET",
          headers:{
            "Authorization":token
          }
        }
      ).then(res=>res.json()).then(res=>{
        if(res.code==200){
          this.loginstatus=true
          this.email=res.data.data.user.email
          this.token=token
        }else{
          this.loginstatus=false
          this.email=''
          this.token=''
          localStorage.setItem('token',null)
        }
      })
    },
    formcheck(){
      if(this.form.password.length<6){
        this.$message.error('Password is too short!');
        return false
      }
      return true
    },
    signin(){
      if (this.formcheck()==false)return
      let sha256 = require("js-sha256").sha256
      let formdata=new FormData()
      formdata.append("email",this.form.email)
      formdata.append("password",sha256(this.form.password))
      fetch("http://localhost:9090/api/auth/register",
        {
          method:"POST",
          body:formdata
        }
      ).then(res=>res.json()).then(res=>{
        if(res.code!=200){
          this.$message.error(res.msg)
        }else{
          this.$message.success(res.msg)
        }
      })
    },
    signup(){
      if (this.formcheck()==false)return
      let sha256 = require("js-sha256").sha256
      let formdata=new FormData()
      formdata.append("email",this.form.email)
      formdata.append("password",sha256(this.form.password))
      fetch("http://localhost:9090/api/auth/login",
        {
          method:"POST",
          body:formdata
        }
      ).then(res=>res.json()).then(res=>{
        if(res.code!=200){
          this.$message.error(res.msg)
        }else{
          this.$message.success(res.msg)
          this.token="Bearer "+res.data.token
          localStorage.setItem('token',this.token)
          this.email=this.form.email
          this.loginstatus=true
          this.authFormVisible=false
        }
      })
    },
    logout(){
      this.loginstatus=false
      this.email=''
      this.token=''
      localStorage.setItem('token',null)

    }
    
  }
}
</script>