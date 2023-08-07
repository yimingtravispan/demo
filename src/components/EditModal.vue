<script setup>
import { defineProps, defineEmits, ref, watchEffect } from "vue";
import { EditPen, Plus } from "@element-plus/icons-vue";

const props = defineProps({
  add: Boolean,
  user: Object,
});

const emit = defineEmits(["finalize"]);

const display = ref(false);

// function for name input
const _name = props.add ? ref("") : ref(props.user.name);
// function for gender input
const _gender = props.add ? ref("") : ref(props.user.gender);
// function for cell input
const _cell = props.add ? ref("") : ref(props.user.cell);

const Gender = ref("1");

const close = () => {
  display.value = false;
};

const submit = (user) => {
  display.value = false;
  emit("finalize", user);
};

const open = () => {
  if (props.add) {
    _name.value = "";
    _gender.value = "";
    _cell.value = "";
  }
  display.value = true;
};
</script>

<template>
  <el-button
    v-if="add"
    :class="{ add: add }"
    type="primary"
    :icon="Plus"
    @click="open"
    >添加</el-button
  >
  <el-button v-if="!add" type="primary" :icon="EditPen" @click="open"
    >编辑</el-button
  >
  <Teleport to="body">
    <div v-if="display" class="modal-mask">
      <div class="modal-container">
        <div class="modal-header">
          <slot v-if="add">新建用户</slot>
          <slot v-if="!add">编辑用户</slot>
        </div>

        <div class="modal-body">
          <input v-model="_name" />
          <p class="error" v-if="!_name">姓名不可为空</p>
        </div>

        <!--
        <div class="modal-body">
          <div>
            <input
              type="radio"
              name="genderoption"
              value="男"
              v-model="_gender"
            />
            <label>男</label>

            <input
              type="radio"
              name="genderoption"
              value="女"
              v-model="_gender"
            />
            <label>女</label>
          </div>
          <p class="error" v-if="!_gender">姓别不可为空</p>
        </div>
        -->

        <el-radio-group v-model="_gender">
          <el-radio label="男">男</el-radio>
          <el-radio label="女">女</el-radio>
        </el-radio-group>

        <div class="modal-body">
          <input v-model="_cell" />
          <p class="error" v-if="!_cell">联系电话不可为空</p>
        </div>

        <div class="modal-footer">
          <button class="modal-default-button reset" @click="close">
            取消
          </button>
          <button
            class="modal-default-button"
            :disabled="!_name || !_gender || !_cell"
            @click="submit({ name: _name, gender: _gender, cell: _cell })"
          >
            完成
          </button>
        </div>
      </div>
    </div>
  </Teleport>
</template>

<style>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
}

.modal-container {
  width: 300px;
  margin: auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
}

.modal-body {
  margin: 10px 0;
}

.modal-default-button {
  float: right;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-header {
  margin: 0 auto;
  font-size: larger;
  font-style: bold;
  color: #00aaff;
}

.error {
  margin-left: 5px;
  margin-top: -1px;
  margin-bottom: -1px;
  font-style: italic;
  font-size: 12px;
  color: #ff7878;
}

button.add {
  margin-top: 5px;
  margin-bottom: 5px;
  color: black;
  border: none;
  background-color: #74fe96;
}

button.add:hover {
  background-color: #a6febc;
  color: white;
}
</style>
