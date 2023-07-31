<script setup>
import { ref, computed, defineEmits } from "vue";
import New from "../components/NewModal.vue";
import Edit from "../components/EditModal.vue";
import users from "../assets/data.JSON";

const names = ref(users.users);
// change selected to users id
// const selected = ref("");

const emit = defineEmits(["show"]);

// hardcoded
let hardcoded = 10;

const showAddModal = ref(false);
const showEditModal = ref(false);

const search = ref("");
const searchGender = ref("");
const temp = ref({
  name: "",
  gender: "",
});
const selected = ref({
  name: "",
  gender: "",
});
const filteredNames = computed(() =>
  names.value.filter(
    (entry) =>
      entry.name.includes(search.value) &&
      (!searchGender.value || entry.gender == searchGender.value)
  )
);

const date = Date();

function create(newUser) {
  /*
  if (hasValidInput()) {
    names.value.push({ id: users.users.length, name: name.value });
    name.value = "";
    
    if (!names.value(name.value)) {
      names.value.push({id: names.value.length(), name: name.value});
      name.value = "";
    }
    
  }
  */
  const date = new Date();
  const year = date.getFullYear();
  const month = date.getMonth();
  const day = date.getDate();

  names.value.push({
    id: hardcoded,
    name: newUser.name,
    gender: newUser.gender,
    cell: newUser.cell,
    date: year + "-" + month + "-" + day,
  });
  showAddModal.value = false;
  hardcoded += 1;
}

function deleteRow(id) {
  names.value = names.value.filter((entry) => entry.id != id);
}

function show(id) {
  showEditModal.value = true;
  names.value.forEach((e) => {
    if (e.id == id) {
      selected.value = e;
    }
  });
}

const updateName = (value) => {
  temp.value.name = value;
};

const updateGender = (value) => {
  temp.value.gender = value;
};

const updateCell = (value) => {
  temp.value.cell = value;
};

const finalizeUpdate = () => {
  if (temp.value.name) {
    selected.value.name = temp.value.name;
  }
  if (temp.value.gender) {
    selected.value.gender = temp.value.gender;
  }
  if (temp.value.cell) {
    selected.value.cell = temp.value.cell;
  }
  temp.value = { name: "", gender: "", cell: "" };
  showEditModal.value = false;
};

const closeEdit = () => {
  temp.value = { name: "", gender: "" };
  showEditModal.value = false;
};

const resetSearch = () => {
  search.value = "";
  searchGender.value = "";
};

/*
function hasValidInput() {
  return name.value.trim();
}
*/
</script>

<template>
  <div>
    用户名<input v-model.lazy="search" placeholder="Filter" /> 性别<select
      v-model.lazy="searchGender"
    >
      <option value="" selected>全部性别</option>
      <option value="男">男</option>
      <option value="女">女</option>
    </select>
    <button @click="resetSearch">重置</button>
  </div>

  <div class="buttons">
    <button @click="showAddModal = true">新建</button>
  </div>

  <Teleport to="body">
    <!-- use the modal component, pass in the prop -->
    <edit
      :show="showEditModal"
      :name="selected.name"
      :gender="selected.gender"
      :cell="selected.cell"
      @update:name="updateName"
      @update:gender="updateGender"
      @update:cell="updateCell"
      @finalize="finalizeUpdate"
      @close="closeEdit"
    >
      <template #header>
        <h3>编辑信息</h3>
      </template>
    </edit>
  </Teleport>

  <Teleport to="body">
    <!-- use the modal component, pass in the prop -->
    <new :show="showAddModal" @create="create" @close="showAddModal = false">
      <template #header>
        <h3>新建用户</h3>
      </template>
    </new>
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
        <button @click="show(n.id)">修改</button>
        <button @click="deleteRow(n.id)">删除</button>
      </tr>
    </tbody>
  </table>
  <p v-if="!filteredNames.length">未找到匹配</p>
</template>

<style>
table {
  border: 2px solid #42b983;
  border-radius: 3px;
  background-color: #fff;
}

th {
  background-color: #42b983;
  color: rgba(255, 255, 255, 0.66);
  cursor: pointer;
  user-select: none;
}

td {
  background-color: #f9f9f9;
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
</style>
