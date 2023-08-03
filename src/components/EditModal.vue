<script setup>
import {
  defineProps,
  defineEmits,
  ref,
  toRef,
  computed,
  watchEffect,
} from "vue";

const props = defineProps({
  add: Boolean,
  show: Boolean,
  name: String,
  gender: String,
  cell: String,
});

const emit = defineEmits([
  "update:name",
  "update:gender",
  "update:cell",
  "close",
  "finalize",
]);

// function for name input
const _name = ref("");
// function for gender input
const _gender = ref("");
// function for cell input
const _cell = ref("");

watchEffect(() => {
  _name.value = props.name;
  _gender.value = props.gender;
  _cell.value = props.cell;
});
</script>

<template>
  <Transition name="modal">
    <div v-if="show" class="modal-mask">
      <div class="modal-container">
        <div class="modal-header">
          <slot v-if="add">新建用户</slot>
          <slot v-if="!add">编辑用户</slot>
        </div>

        <div class="modal-body">
          <input v-model="_name" />
          <p class="error" v-if="!_name">姓名不可为空</p>
        </div>

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

        <div class="modal-body">
          <input v-model="_cell" />
          <p class="error" v-if="!_cell">联系电话不可为空</p>
        </div>

        <!--
        <div class="modal-body">
          <input v-model="_cell" />
        </div>
        -->

        <div class="modal-footer">
          <button class="modal-default-button reset" @click="$emit('close')">
            取消
          </button>
          <button
            class="modal-default-button"
            :disabled="!_name || !_gender || !_cell"
            @click="
              $emit('finalize', { name: _name, gender: _gender, cell: _cell })
            "
          >
            完成
          </button>
        </div>
      </div>
    </div>
  </Transition>
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
  transition: opacity 0.3s ease;
}

.modal-container {
  width: 300px;
  margin: auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 20px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
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

.modal-enter-from {
  opacity: 0;
}

.modal-leave-to {
  opacity: 0;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

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
</style>
