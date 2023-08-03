<script setup>
import { ref, computed } from "vue";
import Edit from "../components/EditModal.vue";
import users from "../assets/data.JSON";

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
    用户名<input v-model="search" placeholder="用户名搜索" /> 性别<select
      v-model="searchGender"
    >
      <option value="" selected>全部性别</option>
      <option value="男">男</option>
      <option value="女">女</option>
    </select>
    <button class="reset" @click="resetSearch">重置</button>
  </div>

  <edit :add="true" @finalize="createUser"></edit>

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
          <edit
            :add="false"
            :user="user"
            @finalize="
              (updatedUser) => (
                (user.name = updatedUser.name),
                (user.gender = updatedUser.gender),
                (user.cell = updatedUser.cell)
              )
            "
          ></edit>
          <button class="del" @click="deleteRow(user.id)">删除</button>
        </td>
      </tr>
    </tbody>
  </table>
  <p v-if="filteredNames.length">总计: {{ filteredNames.length }}</p>
  <p v-if="!filteredNames.length">未找到匹配</p>
</template>

<style>
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
