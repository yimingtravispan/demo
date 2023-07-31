<script setup>
import { defineProps, defineEmits, ref, reactive, toRef } from "vue";

const props = defineProps({
  show: Boolean,
  name: String,
  gender: String,
  cell: String,
});

const emit = defineEmits(["update:gender"]);

const gender = toRef(props, "gender");

function editGender(g) {
  emit("update:gender", g);
}
</script>

<template>
  <Transition name="modal">
    <div v-if="show" class="modal-mask">
      <div class="modal-container">
        <div class="modal-header">
          <slot name="header">修改信息</slot>
        </div>

        <div class="modal-body">
          <input
            :value="name"
            @input="$emit('update:name', $event.target.value)"
          />
        </div>

        <div class="modal-body">
          <div>
            <input
              type="radio"
              name="genderoption"
              value="男"
              v-model="gender"
              @input="editGender($event.target.value)"
            />
            <label>男</label>

            <input
              type="radio"
              name="genderoption"
              value="女"
              v-model="gender"
              @input="editGender($event.target.value)"
            />
            <label>女</label>
          </div>

          <div class="modal-body">
            <input
              :value="cell"
              @input="$emit('update:cell', $event.target.value)"
            />
          </div>
        </div>

        <!--
        <div class="modal-body">
          <input v-model="_cell" />
        </div>
        -->

        <div class="modal-footer">
          <button class="modal-default-button" @click="$emit('close')">
            取消
          </button>
          <button class="modal-default-button" @click="$emit('finalize')">
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
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
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
</style>
