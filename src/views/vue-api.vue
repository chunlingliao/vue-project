<template>
<div>
  <table class="table table-striped">
    <thead>
      <tr>
        <th v-for="(header, i) in headers" :key="i">
          <button v-if="header.type === 'button'" class="btn btn-outline-success" data-toggle="modal" data-target="#exampleModal">
            {{ header.text }}
          </button>
          <template v-else>
            {{ header.text }}
          </template>
        </th>
      </tr>
    </thead>

    <tbody>
      <!-- 在這裡可以選擇性地包括既有資料 -->
      <tr v-for="(item, i) in list" :key="i">
        <td>{{ item.id }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.birthday }}</td>
        <td>{{ item.age }}</td>
        <td>
        <button class="btn btn-outline-info mr-2" data-toggle="modal" data-target="#updateModal" @click="getRow(item.id)">修改</button>
        <button class="btn btn-outline-warning" @click="deleteRow(item.id)">刪除</button>
        </td>
      </tr>
    </tbody>
  </table>

  <!-- 新增彈窗 -->
  <div class="modal fade" id="exampleModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">新增資料</h5>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">name</span>
            </div>
            <input v-model="addUserItems.data.name" type="text" value="" id="name" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">birthday</span>
            </div>
            <input v-model="addUserItems.data.birthday" type="text" value="" id="birthday" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">age</span>
            </div>
            <input v-model="addUserItems.data.age" type="text" value="" id="age" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">關閉</button>
          <button type="submit" class="btn btn-primary" @click="addRow()">完成</button>
        </div>
      </div>
    </div>
  </div>

  <!-- 修改彈窗 -->
  <div class="modal fade" id="updateModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLabel">編輯資料</h5>
          <button type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">name</span>
            </div>
            <input v-model="editUserItems.data.name" type="text" value="" id="updatename" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">birthday</span>
            </div>
            <input v-model="editUserItems.data.birthday" type="text" value="" id="updatebirthday" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
          <div class="input-group mb-3">
            <div class="input-group-prepend">
              <span class="input-group-text" id="inputGroup-sizing-default">age</span>
            </div>
            <input v-model="editUserItems.data.age" type="text" value="" id="updateage" class="form-control" aria-label="Sizing example input" aria-describedby="inputGroup-sizing-default">
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">關閉</button>
          <button type="submit" id="updatedowm" class="btn btn-primary" @click="updateRow(editUserItems.data.id)">完成</button>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import $ from 'jquery'
import axios from 'axios'
import qs from 'qs'
// import _ from "lodash"

export default {
  name: "",
  props: {},
  data: function() {
    // 資料
    return {
      headers:[
        { text: '#', align: 'center'},
        { text: '姓名', align: 'center' },
        { text: '生日', align: 'center'},
        { text: '年齡', align: 'center'},
        { text: '新增', type:'button',align: 'center'}
      ],
      list:[
        // { id: 1, name:'Mark', birthday:'1/11', age:'20'},
        // { id: 2, name:'Jacob', birthday:'2/22', age:'22'},
        // { id: 3, name:'Larry', birthday:'3/33', age:'33'}
      ],
      addUserItems: {
        data: {
          name:'',
          birthday:'',
          age:''
        }
      },
      // 編輯修改
      editUserItems: {
        data: {
          id:'',
          name:'',
          birthday:'',
          age:''
        }
      },
    };
  },
  watch: {
    //監聽值
  },
  computed: {
    //相依的資料改變時才做計算方法
  },
  methods: {
    // 初始
    getList() {
      // console.log(this.list)
        axios.get("http://127.0.0.1:880/api/list.php").then(res => {
          // this.list = res.data.data;
          console.log(this.list)
          let items = []
          res.data.data.forEach(el => {
            let obj = {}
            el.forEach((e,i) => {
              console.log(e,i)
              if (i === 0) obj['id'] = e
              if (i === 1) obj['name'] = e
              if (i === 2) obj['birthday'] = e
              if (i === 3) obj['age'] = e
            })
            items.push(obj)
          })
          console.log('items >>>', items)
          this.list = items;
          // this.itemsAll = res.data.data;
        });
      },
    // 新增
    addRow() {
      this.list.push({
        id: this.list.length + 1 ,
        name: this.addUserItems.data.name,
        birthday: this.addUserItems.data.birthday,
        age: this.addUserItems.data.age
      })
      let data = qs.stringify({
        name: this.addUserItems.data.name,
        birthday: this.addUserItems.data.birthday,
        age: this.addUserItems.data.age
        })
      axios.post("http://127.0.0.1:880/api/add.php", data)
        .then(res => {
          console.log(res);
          this.list.push({
            name: this.addUserItems.data.name,
            birthday: this.addUserItems.data.birthday,
            age: this.addUserItems.data.age
          });
          this.getList()
        });
      $('#exampleModal').modal('hide')
    },
    // 修改
    getRow(id) {
      console.log(this.list)
      this.list.forEach(el => {
        if(id === el.id){
          this.editUserItems.data.id = el.id
          this.editUserItems.data.name = el.name
          this.editUserItems.data.birthday = el.birthday
          this.editUserItems.data.age = el.age
        }
      });
    },
    // 修改完成
    updateRow(id){
      console.log(this.editUserItems)
      let data = qs.stringify({
          id: id,
          name: this.editUserItems.data.name,
          birthday: this.editUserItems.data.birthday,
          age: this.editUserItems.data.age
        });
      console.log(id)
      axios.post("http://127.0.0.1:880/api/update.php", data)
      .then(res => {
      console.log(res)
      // let obj = _.cloneDeep(this.list); //需拷貝舊的值來用
      this.list.forEach((el, i) => {
        console.log(el, i)
        if(id === el.id){
          console.log('el',el.id)
          this.list[i].name = this.editUserItems.data.name
          this.list[i].birthday = this.editUserItems.data.birthday
          this.list[i].age = this.editUserItems.data.age
          }
        });
      });
      $('#updateModal').modal('hide')
    },
    //刪除
    deleteRow(id) {
      console.log(id,this.list)
      this.list.forEach((el, i) => {
        console.log(el, i)
        if(id === el.id){
          console.log('el',el.id)
          console.log(this.list[i])
          this.list.splice(i,1)
        }
      });
    }
  },
  //BEGIN--生命週期
  beforeCreate: function() {
    //實體初始化
  },
  created: function() {
    //實體建立完成。資料 $data 已可取得，但 $el 屬性還未被建立。
    this.src = this.$options.__file ;
    this.getList()
  },
  beforeMount: function() {
    //執行元素掛載之前。
  },
  mounted: function() {
    //元素已掛載， $el 被建立。
  },
  beforeUpdate: function() {
    //當資料變化時被呼叫，還不會描繪 View。
  },
  updated: function() {
    //當資料變化時被呼叫，還不會描繪 View。
  },
  beforeDestroy: function() {
    //實體還可使用。
  },
  destroyed: function() {
    //實體銷毀。
  }
  //END--生命週期
};
</script>

<style lang="scss" scoped>
</style>