<template> 
  <div class="app-container">
    <el-card class="filter-container" shadow="never">
      <div>
        <i class="el-icon-search"></i>
        <span>Filter and Search</span>
        <el-button
          style="float:right"
          @click="handleResetSearch()"
          size="small">
          Reset
        </el-button>
        <el-button
          style="float:right;margin-right: 15px"
          type="primary"
          @click="handleSearchList()"
          size="small">
          Search
        </el-button>
      </div>
      <div style="margin-top: 15px">
        <el-form :inline="true" :model="listQuery" size="small" label-width="140px">
          <el-form-item label="Input Search:">
            <el-input style="width: 203px" v-model="listQuery.keyword" class="input-width" placeholder="Username" clearable></el-input>
          </el-form-item>
        </el-form>
      </div>
    </el-card>
    <el-card class="operate-container" shadow="never">
      <i class="el-icon-tickets"></i>
      <span>Customers</span>
      <el-button size="mini" class="btn-add" @click="handleAdd()" style="margin-left: 20px">Add</el-button>
    </el-card>
    <div class="table-container">
      <el-table ref="adminTable"
                :data="list"
                style="width: 100%;"
                v-loading="listLoading" border>
        <el-table-column label="ID" width="100" align="center">
          <template slot-scope="scope">{{scope.row.id}}</template>
        </el-table-column>
        <el-table-column label="Username" align="center">
          <template slot-scope="scope">{{scope.row.username}}</template>
        </el-table-column>
        <el-table-column label="Birthday" align="center">
          <template slot-scope="scope">{{scope.row.birthday}}</template>
        </el-table-column>
        <el-table-column label="Interests" align="center">
          <template slot-scope="scope">{{scope.row.interests}}</template>
        </el-table-column>
        <!-- <el-table-column label="Account" align="center">
          <template slot-scope="scope">{{scope.row.username}}</template>
        </el-table-column>
        <el-table-column label="Name" align="center">
          <template slot-scope="scope">{{scope.row.nickName}}</template>
        </el-table-column> -->
        <el-table-column label="Email" align="center">
          <template slot-scope="scope">{{scope.row.email}}</template>
        </el-table-column>
        <!-- <el-table-column label="Added At" width="160" align="center">
          <template slot-scope="scope">{{scope.row.createTime | formatDateTime}}</template>
        </el-table-column>
        <el-table-column label="Last Login" width="160" align="center">
          <template slot-scope="scope">{{scope.row.loginTime | formatDateTime}}</template>
        </el-table-column>
        <el-table-column label="Enabled" width="140" align="center">
          <template slot-scope="scope">
            <el-switch
              @change="handleStatusChange(scope.$index, scope.row)"
              :active-value="1"
              :inactive-value="0"
              v-model="scope.row.status">
            </el-switch>
          </template>
        </el-table-column> -->
        <el-table-column label="Action" width="180" align="center">
          <template slot-scope="scope">
            <!-- <el-button size="mini"
                       type="text"
                       @click="handleSelectRole(scope.$index, scope.row)">Assign Roles
            </el-button> -->
            <el-button size="mini"
                       type="text"
                       @click="handleUpdate(scope.$index, scope.row)">
              Edit
            </el-button>
            <el-button size="mini"
                       type="text"
                       @click="handleDelete(scope.$index, scope.row)">Delete
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </div>
    <div class="pagination-container">
      <el-pagination
        background
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        layout="total, sizes,prev, pager, next,jumper"
        :current-page.sync="listQuery.pageNum"
        :page-size="listQuery.pageSize"
        :page-sizes="[5,10,15]"
        :total="total">
      </el-pagination>
    </div>
    <el-dialog
      :title="isEdit?'Edit User':'Add User'"
      :visible.sync="dialogVisible"
      width="40%">
      <el-form :model="admin"
               :rules="rules" 
               ref="adminForm"
               label-width="150px" size="small">
        <el-form-item label="ID:"  prop="id">
          <el-input v-model="admin.id" :disabled="isEdit" style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Username:" prop="username">
          <el-input v-model="admin.username" style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Birthday:" prop="birthday">
          <el-input v-model="admin.birthday" style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Interests:">
          <el-input v-model="admin.interests" style="width: 250px"></el-input>
        </el-form-item>
        <!-- <el-form-item label="Account:">
          <el-input v-model="admin.username" style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Name:">
          <el-input v-model="admin.nickName" style="width: 250px"></el-input>
        </el-form-item> -->
        <el-form-item label="Email:">
          <el-input v-model="admin.email" style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Password:" prop="password">
          <el-input v-model="admin.password"  type="password" style="width: 250px"></el-input>
        </el-form-item>
        <!-- <el-form-item label="Note:">
          <el-input v-model="admin.note"
                    type="textarea"
                    :rows="5"
                    style="width: 250px"></el-input>
        </el-form-item>
        <el-form-item label="Enabled:">
          <el-radio-group v-model="admin.status">
            <el-radio :label="1">Yes</el-radio>
            <el-radio :label="0">No</el-radio>
          </el-radio-group>
        </el-form-item> -->
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false" size="small">Cancel</el-button>
        <el-button type="primary" @click="handleDialogConfirm()" size="small">Confirm</el-button>
      </span>


    </el-dialog>
    <el-dialog
      title="Assign Roles"
      :visible.sync="allocDialogVisible"
      width="30%">
      <el-select v-model="allocRoleIds" multiple placeholder="Select" size="small" style="width: 80%">
        <el-option
          v-for="item in allRoleList"
          :key="item.id"
          :label="item.name"
          :value="item.id">
        </el-option>
      </el-select>
      <span slot="footer" class="dialog-footer">
        <el-button @click="allocDialogVisible = false" size="small">Cancel</el-button>
        <el-button type="primary" @click="handleAllocDialogConfirm()" size="small">Confirm</el-button>
      </span>
    </el-dialog>
  </div>
</template>
<script>
  import {fetchList,createAdmin,updateAdmin,updateStatus,deleteAdmin,getRoleByAdmin,allocRole} from '@/api/login';
  import {fetchAllRoleList} from '@/api/role';
  import {formatDate} from '@/utils/date';

  const defaultListQuery = {
    pageNum: 1,
    pageSize: 10,
    keyword: null
  };
  const defaultAdmin = {
    id: null,
    username: null,
    password: null,
    nickName: null,
    email: null,
    note: null,
    status: 1
  };
  export default {
    name: 'adminList',
    data() {
      return {
        listQuery: Object.assign({}, defaultListQuery),
        list: null,
        total: null,
        listLoading: false,
        dialogVisible: false,
        admin: Object.assign({}, defaultAdmin),
        isEdit: false,
        allocDialogVisible: false,
        allocRoleIds:[],
        allRoleList:[],
        allocAdminId:null,
        rules: {
          id: [
            { required: true, message: 'Please input ID', trigger: 'blur' },
            { pattern: /^[1-9]\d*$/, message: 'ID must be a positive integer', trigger: 'blur' }
          ],
          username: [
            { required: true, message: 'Please input Username', trigger: 'blur' },
          ],
          birthday: [
            { pattern: /^\d{4}-\d{2}-\d{2}$/, message: 'Birthday must be in YYYY-MM-DD format', trigger: 'blur'}
          ],
          password: [
            { required: true, message: 'Please input Password', trigger: 'blur' },
            { min: 3, message: 'Password must be at least 3 characters long', trigger: 'blur' }
          ]
        },
      }
    },
    created() {
      this.getList();
      this.getAllRoleList();
    },
    filters: {
      formatDateTime(time) {
        if (time == null || time === '') {
          return 'N/A';
        }
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm:ss')
      }
    },
    methods: {
      handleResetSearch() {
        this.listQuery = Object.assign({}, defaultListQuery);
      },
      handleSearchList() {
        this.listQuery.pageNum = 1;
        this.getList();
      },
      handleSizeChange(val) {
        this.listQuery.pageNum = 1;
        this.listQuery.pageSize = val;
        this.getList();
      },
      handleCurrentChange(val) {
        this.listQuery.pageNum = val;
        this.getList();
      },
      handleAdd() {
        this.dialogVisible = true;
        this.isEdit = false;
        this.admin = Object.assign({},defaultAdmin);
      },
      handleStatusChange(index, row) {
        this.$confirm('Are you sure you want to change this status?', 'Prompt', {
          confirmButtonText: 'Confirm',
          cancelButtonText: 'Cancel',
          type: 'warning'
        }).then(() => {
          updateStatus(row.id, {status: row.status}).then(response => {
            this.$message({
              type: 'success',
              message: 'Modified successfully!'
            });
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: 'Canceled modification'
          });
          this.getList();
        });
      },
      handleDelete(index, row) {
        this.$confirm('Are you sure you want to delete this user?', 'Prompt', {
          confirmButtonText: 'Confirm',
          cancelButtonText: 'Cancel',
          type: 'warning'
        }).then(() => {
          //数据库
          debugger
          fetch(this.$store.state.user.globalURL+'/interface22', {
              method: 'POST',
              headers: {
                'Content-Type': 'application/json'
              },
              body: JSON.stringify(
                {"UserId": row.id}
              )
            }).then(response => {
              debugger
              return response.json();  // 解析 JSON 数据
            }).then(data => {
              this.$message({
                message: 'Deleted successfully',
                type: 'success',
                duration: 1000
              });
            }).catch(error => {
              console.error('Error during deletion:', error);
              this.$message.error('Server error');
            });
          
          this.getList();
          //const blob = new Blob([JSON.stringify({"ItemId": row.id})],
          //                      {type: 'application/json'});
          //window.open(URL.createObjectURL(blob));
          // const a = document.createElement('a');
          // a.href = URL.createObjectURL(blob);
          // a.download = 'output.json';
          // a.click();
          // URL.revokeObjectURL(a.href);
          /*this.$message({
            message: 'Deleted successfully',
            type: 'success',
            duration: 1000
          }); */
          
          // deleteAdmin(row.id).then(response => {
          //   this.$message({
          //     type: 'success',
          //     message: 'Deleted successfully!'
          //   });
          //   this.getList();
          // });
        });
      },
      handleUpdate(index, row) {
        this.dialogVisible = true;
        this.isEdit = true;
        this.admin = Object.assign({},row, { password: '' });
      },
      mapOutputData(item) {
        return {
          UserId: item.id,
          UserName: item.username,
          UserPassword: item.password,
          Birthday: item.birthday,
          Interests: item.interests,
          Email: item.email
        };
      },
      handleDialogConfirm() {
        this.$confirm('Are you sure you want to confirm?', 'Prompt', {
          confirmButtonText: 'Confirm',
          cancelButtonText: 'Cancel',
          type: 'warning'
        }).then(() => {

          // if (this.isEdit) {
          //   updateAdmin(this.admin.id,this.admin).then(response => {
          //     this.$message({
          //       message: 'Modified successfully!',
          //       type: 'success'
          //     });
          //     this.dialogVisible =false;
          //     this.getList();
          //   })
          // }
          // else
          this.$refs.adminForm.validate((valid) => {
            if (valid) {
            //数据库
            debugger
            fetch(this.$store.state.user.globalURL+'/interface21', {
              method: 'POST',
              headers: {
                'Content-Type': 'application/json'
              },
              body: JSON.stringify(
                this.mapOutputData(this.admin)
              )
            }).then(response => {
              debugger
              return response.json();  // 解析 JSON 数据
            }).then(data => {
              this.$message({
                message: 'Submitted successfully',
                type: 'success',
                duration: 1000
              });
            }).catch(error => {
              console.error('Error during updating:', error);
              this.$message.error('Server error');
            });
            this.dialogVisible = false;
            this.getList();
            // const blob = new Blob([JSON.stringify(this.mapOutputData(this.admin))],
            //                       {type: 'application/json'});
            // window.open(URL.createObjectURL(blob));
            // const a = document.createElement('a');
            // a.href = URL.createObjectURL(blob);
            // a.download = 'output.json';
            // a.click();
            // URL.revokeObjectURL(a.href);
            // this.$message({
            //   message: 'Submitted successfully',
            //   type: 'success',
            //   duration: 1000
            // });
            // createAdmin(this.admin).then(response => {
            //   this.$message({
            //     message: 'Added successfully!',
            //     type: 'success'
            //   });
            //   this.dialogVisible =false;
            //   this.getList();
            // })
          } else {
            this.$message({
              type: 'error',
              message: 'One or more fields have invalid entries, please check the form again'
            });
            return false;
          }});
        })
      },
      handleAllocDialogConfirm(){
        this.$confirm('Are you sure you want to confirm?', 'Prompt', {
          confirmButtonText: 'Confirm',
          cancelButtonText: 'Cancel',
          type: 'warning'
        }).then(() => {
          let params = new URLSearchParams();
          params.append("adminId", this.allocAdminId);
          params.append("roleIds", this.allocRoleIds);
          allocRole(params).then(response => {
            this.$message({
              message: 'Allocated successfully!',
              type: 'success'
            });
            this.allocDialogVisible = false;
          })
        })
      },
      handleSelectRole(index,row){
        this.allocAdminId = row.id;
        this.allocDialogVisible = true;
        this.getRoleListByAdmin(row.id);
      },
      mapInputData(items) {
        return items.map(item => ({
          id: item.UserId,
          username: item.UserName,
          password: item.UserPassword,
          birthday: item.Birthday,
          interests: item.Interests,
          email: item.Email
        }));
      },
      getList() {
        debugger
        this.listLoading = true;
        fetchList(this.listQuery).then(response => {
          this.listLoading = false;
          // this.list = response.data.list;
          // this.total = response.data.total;
          //数据库
          // this.list = this.mapInputData(require('@/public/1/customer.json'));
          fetch(this.$store.state.user.globalURL+'/interface20', {
              method: 'POST',
              headers: {
                'Content-Type': 'application/json'
              },
              body: JSON.stringify({})
            }).then(response => {
              debugger
              return response.json();  // 解析 JSON 数据
            }).then(data => {
              let filteredData = this.mapInputData(data.UserData);
              debugger
              if (this.listQuery.keyword) {
                filteredData = filteredData.filter(item => item.username.includes(this.listQuery.keyword));
              }
              this.total = filteredData.length;

              const start = (this.listQuery.pageNum - 1) * this.listQuery.pageSize;
              const end = start + this.listQuery.pageSize;
              this.list = filteredData.slice(start, end);
              debugger
            }).catch(error => {
              this.$message.error('Server error');
            });
          debugger
        });
      },
      getAllRoleList() {
        fetchAllRoleList().then(response => {
          this.allRoleList = response.data;
        });
      },
      getRoleListByAdmin(adminId) {
        getRoleByAdmin(adminId).then(response => {
          let allocRoleList = response.data;
          this.allocRoleIds=[];
          if(allocRoleList!=null&&allocRoleList.length>0){
            for(let i=0;i<allocRoleList.length;i++){
              this.allocRoleIds.push(allocRoleList[i].id);
            }
          }
        });
      }
    }
  }
</script>
<style></style>