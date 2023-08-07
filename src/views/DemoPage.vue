<script setup>
import { ref, computed } from "vue";
import EditModal from "../components/EditModal.vue";
import users from "../assets/data.JSON";
import { RefreshLeft, Delete } from "@element-plus/icons-vue";

// Getting data
const names = ref(users.users);
// vars
let hardcoded = 10;

// For searching
const search = ref("");
const searchGender = ref("");
const resetSearch = () => {
  search.value = "";
  searchGender.value = "";
};

// Grid Display
const filteredNames = computed(() =>
  names.value.filter(
    (entry) =>
      entry.name.includes(search.value) &&
      (!searchGender.value || entry.gender == searchGender.value)
  )
);

// Grid Deletion
function deleteRow(id) {
  names.value = names.value.filter((entry) => entry.id != id);
}

// Create User
const createUser = (user) => {
  const date = new Date();
  const year = date.getFullYear();
  const month = date.getMonth() + 1;
  const day = date.getDate();

  names.value.push({
    id: hardcoded,
    name: user.name,
    gender: user.gender,
    cell: user.cell,
    date: year + "-" + month + "-" + day,
  });
  hardcoded += 1;
};
</script>

<template>
  <div>
    用户名
    <el-input v-model="search" placeholder="用户名搜索" clearable />
    性别

    <el-select v-model="searchGender" placeholder="性别搜索" clearable>
      <el-option
        v-for="item in users.genders"
        :key="item.value"
        :label="item.label"
        :value="item.value"
      />
    </el-select>

    <el-button class="reset" @click="resetSearch" :icon="RefreshLeft"
      >重置</el-button
    >
  </div>

  <EditModal :add="true" @finalize="createUser"></EditModal>

  <table>
    <thead>
      <tr>
        <th v-for="col in users.cols" :key="col.en">{{ col.cn }}</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="user in filteredNames" :key="user.id">
        <td v-for="col in users.cols" :key="col">
          {{ user[col.en] }}
        </td>
        <td>
          <EditModal
            :add="false"
            :user="user"
            @finalize="
              (updatedUser) => (
                (user.name = updatedUser.name),
                (user.gender = updatedUser.gender),
                (user.cell = updatedUser.cell)
              )
            "
          ></EditModal>
          <el-button class="del" @click="deleteRow(user.id)" :icon="Delete">
            删除
          </el-button>
        </td>
      </tr>
    </tbody>
  </table>
  <p v-if="filteredNames.length">总计: {{ filteredNames.length }}</p>
  <p v-if="!filteredNames.length">未找到匹配</p>

  <el-table
    class="el-table"
    :data="filteredNames"
    style="width: 100% !important"
  >
    <el-table-column label="编号" prop="id" />
    <el-table-column label="用户名" prop="name" />
    <el-table-column label="性别" prop="gender" />
    <el-table-column label="手机号码" prop="cell" />
    <el-table-column label="添加日期" prop="date" />
    <el-table-column label="操作">
      <template #default="scope">
        <EditModal
          :add="false"
          :user="scope.row"
          @finalize="
            (updatedUser) => (
              (scope.row.name = updatedUser.name),
              (scope.row.gender = updatedUser.gender),
              (scope.row.cell = updatedUser.cell)
            )
          "
        ></EditModal>
        <el-button type="danger" @click="deleteRow(scope.row.id)" :icon="Delete"
          >删除</el-button
        >
      </template>
    </el-table-column>
  </el-table>

  <p v-if="filteredNames.length">总计: {{ filteredNames.length }}</p>
  <p v-if="!filteredNames.length">未找到匹配</p>
</template>

<style>
.el-input {
  width: fit-content;
}

.el-select {
  width: fit-content;
}

table {
  margin: auto;
  border-collapse: collapse;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #e4e4e4;
  color: #464646;
}

td {
  border: 1px solid #e4e4e4;
  background-color: #ffffff;
}

th,
td {
  min-width: 120px;
  padding: 10px 20px;
}

th.active {
  color: #fff;
}

th.active .arrow {
  opacity: 1;
}

button:disabled {
  background-color: #c0c0c0;
  color: white;
  cursor: auto;
  text-decoration: line-through;
}

button:disabled:hover {
  background-color: #c0c0c0;
  color: white;
}

button {
  margin-left: 5px;
  padding: 3px 10px;
  border: none;
  border-radius: 5px;
  background-color: #98ddff;
  cursor: pointer;
}

button.new {
  border-radius: 10px;
  padding: 5px 10px;
  margin-top: 5px;
  margin-bottom: 5px;
  background-color: #84ffa3;
}

button.new:hover {
  background-color: #009b27;
  color: white;
}

button.reset {
  background-color: #7f7f7f;
  color: white;
}

button.reset:hover {
  background-color: #cecece;
  color: black;
}

button.del {
  background-color: #ffa3a3;
}

button.del:hover {
  background-color: #ff5353;
  color: white;
}

button:hover {
  background-color: #0093dc; /* Blue background on hover */
  color: #ffffff;
}

input {
  margin-left: 5px;
  margin-right: 10px;
  border: 1px solid;
  border-radius: 5px;
}

select {
  margin-left: 5px;
  margin-right: 10px;
  border: 1px solid;
  border-radius: 5px;
}

p {
  margin-block-start: 0;
  margin-block-end: 0;
}
</style>
