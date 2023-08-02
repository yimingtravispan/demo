<script setup>
import { ref, computed, defineEmits } from "vue";
import Edit from "../components/EditModal.vue";
import users from "../assets/data.JSON";
import { selectGroupKey } from "element-plus";

const names = ref(users.users);
// change selected to users id
// const selected = ref("");

const emit = defineEmits(["show"]);

// hardcoded
let hardcoded = 10;

const isAdd = ref(false);
const showAddModal = ref(false);
const showEditModal = ref(false);

const search = ref("");
const searchGender = ref("");

const selected = ref({
  name: "",
  gender: "",
  cell: "",
});

const filteredNames = computed(() =>
  names.value.filter(
    (entry) =>
      entry.name.includes(search.value) &&
      (!searchGender.value || entry.gender == searchGender.value)
  )
);

const date = Date();

function deleteRow(id) {
  names.value = names.value.filter((entry) => entry.id != id);
}

function select(id) {
  showEditModal.value = true;
  names.value.forEach((e) => {
    if (e.id == id) {
      selected.value = e;
    }
  });
}

const finalizeUpdate = (user) => {
  if (isAdd.value) {
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
  } else {
    if (user.name) {
      selected.value.name = user.name;
    }
    if (user.gender) {
      selected.value.gender = user.gender;
    }
    if (user.cell) {
      selected.value.cell = user.cell;
    }
  }
  close();
};

const close = () => {
  selected.value = { name: "", gender: "", cell: "" };
  isAdd.value = false;
  showEditModal.value = false;
};

const resetSearch = () => {
  search.value = "";
  searchGender.value = "";
};
</script>

<template>
  <div>
    用户名<input v-model="search" placeholder="Filter" /> 性别<select
      v-model="searchGender"
    >
      <option value="" selected>全部性别</option>
      <option value="男">男</option>
      <option value="女">女</option>
    </select>
    <button class="reset" @click="resetSearch">重置</button>
  </div>

  <div>
    <button
      class="new"
      @click="
        showEditModal = true;
        isAdd = true;
      "
    >
      新建
    </button>
  </div>

  <Teleport to="body">
    <!-- use the modal component, pass in the prop -->
    <edit
      :add="isAdd"
      :show="showEditModal"
      :name="selected.name"
      :gender="selected.gender"
      :cell="selected.cell"
      @finalize="finalizeUpdate"
      @close="close"
    >
    </edit>
  </Teleport>

  <table>
    <thead>
      <tr>
        <th v-for="col in users.cols" :key="col.en">{{ col.cn }}</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="n in filteredNames" :key="n.id">
        <td v-for="col in users.cols" :key="col">
          {{ n[col.en] }}
        </td>
        <td>
          <button @click="select(n.id)">修改</button>
          <button class="del" @click="deleteRow(n.id)">删除</button>
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
</style>
