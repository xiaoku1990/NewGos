<template>
  <div class="header">


        <img src="~@/assets/images/logo.png" />
        <div class="grid-row">

            <span class="pjt-name">GOS后台管理系统  (<span style="color: #0000ff;">{{inNetMessage}}</span>) </span>
          <div class="header-right">
            <el-col class="header-admin">

              <el-badge :value=badge class="item">
                 <img src="~@/assets/images/avatar.png" class="admin-icon"/>
              </el-badge>

              <el-dropdown @command="handleDropDown">

                  <span class="el-dropdown-link">{{adminName}}
                       <i class="el-icon-arrow-down el-icon--right"></i>
                </span>


                <el-dropdown-menu slot="dropdown">
                  <!--<el-dropdown-item>-->

                  <!--</el-dropdown-item>-->
                  <!--<el-dropdown-item>设置</el-dropdown-item>-->
                  <el-badge :value=badge>
                    <!--消息-->
                    <el-dropdown-item command="bgeInfo">推送列表</el-dropdown-item>
                  </el-badge>
                  <el-dropdown-item command="modify">修改密码</el-dropdown-item>
                  <el-dropdown-item command="exitLog">退出</el-dropdown-item>


                </el-dropdown-menu>
              </el-dropdown>
            </el-col>
          </div>
        </div>

    <!--退出-->
    <el-dialog
      title="退出登录"
      :append-to-body='true' :lock-scroll="true" :modal="true"
      :close-on-click-modal="false"
      :visible.sync="exitVehicle"
      top="35vh"
      width="30%">
      <p>确定要退出系统吗？</p>
      <span slot="footer" class="dialog-footer">
        <el-button @click="exitVehicle = false" type="text">取 消</el-button>
        <el-button type="primary" size="small" round @click="exitNotLog">确 定</el-button>
      </span>
    </el-dialog>
    <!--修改密码-->
    <el-dialog
      title="修改密码"
      :append-to-body='true' :lock-scroll="true" :modal="true"
      :close-on-click-modal="false"
      :visible.sync="modifyDialog"
      width="30%"
      top="35vh">
      <el-form ref="modifyform" :rules="rules" :model="modifyform" label-width="88px">
        <el-form-item label="原密码" prop="old_pwd">
          <el-input v-model="modifyform.old_pwd"></el-input>
        </el-form-item>
        <el-form-item label="新密码" prop="new_pwd">
          <el-input type="password" v-model="modifyform.new_pwd"></el-input>
        </el-form-item>
        <el-form-item label="确认新密码" prop="new_pwd_repeat">
          <el-input type="password" v-model="modifyform.new_pwd_repeat"></el-input>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button @click="modifyDialog = false" type="text">取 消</el-button>
        <el-button type="primary" :loading="modifyLoading" size="small" round @click="modifyClick">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
    //import {add} from "../../store/actions";//利用actions 调用mutations 中的方法重大失误
    import  {mapActions} from 'vuex'
    export default {
      name: "headers",
      data(){
        let old_pwd = (rule, value, callback) => {
          if (value === '') {
            callback(new Error('请输入原密码'));
          } else {
            callback();
          }
        };
        let new_pwd = (rule, value, callback) => {
          if (value === '') {
            callback(new Error('请输入新密码'));
          } else {
            callback();
          }
        };
        let new_pwd_repeat = (rule, value, callback) => {
          if (value === '') {
            callback(new Error('请再次输入新密码'));
          } else if (value !== this.modifyform.new_pwd) {
            callback(new Error('两次输入密码不一致!'));
          } else {
            callback();
          }
        };
        return{
          adminName: '[未登录]',
          userInfo: null,
          exitVehicle: false,
          modifyDialog: false,
          modifyLoading: false,
          inNet:'', //内外网
          inNetMessage:'', //内外网信息
          badge:'',//消息推送的记录数
          modifyform:{
            build_id: this.$BuildId,
            nick_name: '',
            user_id: '',
            old_pwd: '',
            new_pwd: '',
            new_pwd_repeat: ''
          },
          rules: {
            old_pwd: [
              { validator: old_pwd, trigger: 'blur' }
            ],
            new_pwd: [
              { validator: new_pwd, trigger: 'blur' }
            ],
            new_pwd_repeat: [
              { validator: new_pwd_repeat, trigger: 'blur' }
            ]
          }
        }
      },
      created(){
        let _this = this;
        console.log(_this.inNet);
        if(_this.inNet){
          _this.inNetMessage = '内网';
        }else {
          _this.inNetMessage = '外网';
        };//
        eventbus.$on('OnToInNet',function (date,build) {
          console.log('header!!!!');
          console.log(_this.inNet);
          if (date) {
            _this.inNetMessage = '内网';
          }else{
            _this.inNetMessage = '外网';
          };//

        })
      },
      mounted(){
        const _this = this;
              _this.badge='';//test
              //_this.gettunnelList();//先获取消息推送列表数
        //
        if(localStorage.getItem('userInfo')) {
          // console.log(`从新读取登录信息：${localStorage.getItem('userInfo')}`);
          this.userInfo = JSON.parse(localStorage.getItem('userInfo'));
          this.adminName = (this.userInfo.user_name? this.userInfo.user_name : this.userInfo.nick_name);
        } else {
          // console.log('local storage 里面找不到登录信息userInfo');
        }
        eventbus.$on('onUserLogin', function(userInfo) {
          // console.log('on user login');
          if(userInfo) {
            _this.userInfo = userInfo;
            _this.adminName = (userInfo.user_name? userInfo.user_name : userInfo.nick_name);
          } else {
            _this.userInfo = null;
            _this.adminName = '';
          }
          // console.log(`user name: ${_this.adminName}`);
        });
        //
      },
      methods:{
        handleDropDown(func) {
          // console.log(`下拉菜单触发: ${func}`);
          this[func]();
        },
        exitLog() {
          // console.log('点击退出登录');
          this.exitVehicle = true;
        },
        exitNotLog() {
          eventbus.$emit('onUserLogout');
          localStorage.removeItem('userInfo');  //退出清除缓存
          this.$router.push("/");
          this.exitVehicle = false;
          // console.log('我退出了');
        },
        bgeInfo(){
          eventbus.$emit('tunnelList');
        },
        modify() {
          // console.log('点击修改密码');
          if(this.$refs['modifyform'] !== undefined) {
            this.$refs['modifyform'].resetFields();
          }
          if(this.userInfo) {
            this.modifyDialog = true;
            this.modifyform.old_pwd = '';
            this.modifyform.new_pwd = '';
            this.modifyform.new_pwd_repeat = '';
            this.modifyform.nick_name = this.userInfo.nick_name;
            this.modifyform.user_id = this.userInfo.user_id;
          } else {
            this.$notify({
              title: '修改失败',
              message: '请先登录',
              duration: 2000,
              type: 'error'
            });
          }
        },
        modifyClick() {
          this.$refs['modifyform'].validate((valid) => {
            if (valid) {
              // console.log('我修改密码了');
              this.modifyLoading = true;
              this.$http({
                method: 'post',
                url: `${this.$Api}/GOSSystem/userPwdChange`,
                params: this.modifyform
              }).then(response => {
                this.modifyLoading = false;
                if(response.data.result === 1) {
                  this.$notify({
                    title: '修改成功',
                    message: response.data.msg,
                    duration: 2000,
                    type: 'success'
                  });
                  this.modifyDialog = false;
                } else{
                  this.$notify({
                    title: '修改失败',
                    message: response.data.msg,
                    duration: 2000,
                    type: 'error'
                  });
                }
              }).catch((error) => {
                this.modifyLoading = false;
                this.$notify({
                  title: 'ERROR',
                  message: `${error}`,
                  duration: 2000,
                  type: 'error'
                });
              });
            } else {
              // console.log('字段不对，请检查');
              return false;
            }
          });
        },
        gettunnelList(){
          //如何page 为空就会返回所有列表数;
          const url=this.$Api+'/GOSSystem/getAlarmMsgList';
          let dateTime = new Date();////查询的日期
          const date={
            user_id:'beefind001',
            build_id:this.$BuildId,
            date:dateTime,  //查询的日期
            page:''//
          };
          this.$http({
            method: 'POST',
            url: url,
            data: date
          }).then(response => {

            console.log(response);
            if(response.result==1){
              this.badge='25';//test
              //this.badge=response.result.msg_list.length;
              //eventbus.$emit('tunnelInfo','20');

            };//

          }).catch((error) => {
            this.$notify({
              title: 'ERROR',
              message: `${error}`,
              duration: 2000,
              type: 'error'
            });
          });



        },
      },
    }
</script>

<style scoped lang="scss">
.header{
  width: 100%;
  display: flex;
  position: relative;
  height: 81px;
  button{
    background: transparent;
    color: #666;
    border: none;
    i{
      font-size: 26px;
      position: relative;
      top: 5px;
      margin-right: 3px;
    }
  }
}
  .t-btn{
    position: absolute;
    top: 28px;
    width: 24px;
    height: 26px;
    cursor: pointer;
  }
.t-btn-span{
  position: absolute;
  left: 0;
  width: 24px;
  height: 4px;
  content: '';
  background: #fff;
}
.t-btn-span:before,.t-btn-span:after{
  position: absolute;
  left: 0;
  width: 24px;
  height: 4px;
  content: '';
  background: #fff;
}

.t-btn-span:before{
  top: 0;
  -webkit-transform: translateY(-7px);
  transform: translateY(-7px);
  -webkit-transition: all .3s;
  transition: all .3s;
}
.t-btn-span:after{
  -webkit-transform: translateY(7px);
  transform: translateY(7px);
  -webkit-transition: all .3s;
  transition: all .3s;
}
  .pjt-name{
   font-size: 20px;
    font-weight: 700;
  }
  .grid-row{
    line-height: 81px;
    text-align: center;
    flex: 10 0 auto;
  }
  .grid-row .el-dropdown{
    cursor: pointer;
  }
  .header-right{
    float:right;
    position: absolute;
    right: 0px;
    top: 0px;
    .header-admin{
      margin-left: 20px;
      width: 200px;
      float: right;
      /*border:1px solid #ff0000;*/
      border-left:1px solid #ccc;
    }
    .admin-icon{
      height: 40px;
      width: 40px;
      border-radius: 50px;
      position: relative;
      top: 0px;
      /*border:2px solid #00ff00;*/
    }
  }
.item {
  position: relative;
  top: 15px;
  /*margin-top: 10px;*/
  /*margin-right: 40px;*/
}
</style>
