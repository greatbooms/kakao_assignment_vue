<template>
  <transition name="modal">
    <div class="modal-mask" v-on:click.stop="$emit('close')">
      <div class="modal-wrapper" v-on:click.stop="">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">
              <h3>유저 방 생성</h3>
            </slot>
          </div>

          <div class="modal-body">
            <slot name="body">
              roomId : <input type="text" v-model="roomId" ref="roomId" />
              <br />
              <br />
              userId : <input type="number" v-model="userId" ref="userId" />
            </slot>
          </div>

          <div class="modal-footer">
            <slot name="footer">
              <button class="modal-default-button" @click="roomUserInput">
                생성
              </button>
              <br />
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>
<script>
import axios from "axios";
export default {
  name: "RoomCreateModal",
  data: function () {
    return {
      roomId: "",
      userId: Number,
    };
  },
  methods: {
    roomUserInput() {
      if (this.roomId === "") {
        this.$refs.roomId.focus();
        return false;
      }
      if (this.userId === Number || this.userId === "") {
        this.$refs.userId.focus();
        return false;
      }
      let url = "/api/v1/roomUserInput";
      let data = {};
      let config = {
        headers: {
          "X-USER-ID": this.userId,
          "X-ROOM-ID": this.roomId,
        },
      };
      axios
        .post(url, data, config)
        .then((res) => {
          console.log(res);
          this.$emit("create");
        })
        .catch(function (error) {
          if (error.response) {
            alert(
              `Code : ${error.response.data.errorCode} | Msg : ${error.response.data.errorMsg}`
            );
          } else if (error.request) {
            console.log(error.request);
          } else {
            console.log("Error", error.message);
          }
          console.log(error.config);
        });
    },
  },
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
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
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
