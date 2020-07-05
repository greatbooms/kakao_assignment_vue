<template>
  <transition name="modal">
    <div class="modal-mask" v-on:click.stop="$emit('close')">
      <div class="modal-wrapper" v-on:click.stop="">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">
              <h3>뿌리기 결과 조회</h3>
            </slot>
          </div>

          <div class="modal-body">
            <slot name="body"> </slot>
          </div>

          <div class="modal-footer">
            <slot name="footer"> </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
export default {
  name: "InquiryModal",
};
</script>
<style scoped>
.modal-mask {
  position: fixed;
  z-index: 100;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  transition: opacity 0.3s ease;
  justify-content: center;
  align-items: center;
}
.modal-wrapper {
  position: relative;
  z-index: 101;
}

.modal-container {
  width: 95vw;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
  box-sizing: border-box;
}

.modal-header h3 {
  margin-top: 0;
  color: rgb(255, 231, 56);
  font-weight: bold;
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

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
