<template>
  <div>
    <div id="listTopTitle">채팅</div>
    <RoomList
      v-for="item in roomArray"
      :key="item.id"
      :roomId="item.roomId"
      :peopleCount="item.count"
      v-on:click.native="getRoomUserList(item.roomId)"
    ></RoomList>
    <div id="createRoomUser" @click="openModal">유저, 방 생성</div>
    <RoomCreateModal
      v-if="createModalBool"
      @close="closeModal"
      @create="createModal"
    >
    </RoomCreateModal>
    <UserSelectModal v-if="selectModalBool" @close="closeModal">
      <div
        slot="body"
        class="userBox"
        v-for="user in userArray"
        :key="user.roomUserInfoKey"
      >
        <button
          class="btn"
          v-on:click="goRoom(user.roomUserInfoKey, user.userId, selectedRoomId)"
        >
          {{ user.userId }}
        </button>
        <br />
      </div>
    </UserSelectModal>
  </div>
</template>

<script>
import axios from "axios";
import RoomList from "@/components/RoomList.vue";
import RoomCreateModal from "@/components/RoomCreateModal.vue";
import UserSelectModal from "@/components/UserSelectModal.vue";
export default {
  name: "Home",
  components: {
    RoomList,
    RoomCreateModal,
    UserSelectModal,
  },
  data: function () {
    return {
      roomArray: [],
      userArray: [],
      createModalBool: false,
      selectModalBool: false,
      selectedRoomId: "",
    };
  },
  mounted() {
    this.getRoomList();
  },
  methods: {
    openModal() {
      this.createModalBool = true;
    },
    closeModal() {
      this.createModalBool = false;
      this.selectModalBool = false;
    },
    createModal() {
      this.createModalBool = false;
      this.getRoomList();
    },
    getRoomList() {
      let url = "/api/v2/roomList";
      axios
        .get(url)
        .then((res) => {
          this.roomArray = res.data;
          console.log(res);
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
    getRoomUserList(roomId) {
      console.log(roomId);
      this.selectedRoomId = roomId;
      let url = `/api/v2/roomUserList/${roomId}`;
      axios
        .get(url)
        .then((res) => {
          this.userArray = res.data;
          this.selectModalBool = true;
          console.log(res);
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
    goRoom(roomUserInfoKey, userId, roomId) {
      console.log(userId);
      console.log(roomId);
      const moveUrl = "Room";
      const params = {
        roomUserInfoKey: roomUserInfoKey,
        userId: userId,
        roomId: roomId,
      };
      this.$router.push({ name: moveUrl, params: params });
    },
  },
};
</script>

<style>
#app {
  width: 100vw;
  height: 100%;
  box-sizing: border-box;
  margin-bottom: 5vh;
}
#listTopTitle {
  width: 94vw;
  height: 5vh;
  padding: 1vh 3vw;
  display: flex;
  align-items: center;
  font-weight: bold;
  font-size: 120%;
}
#createRoomUser {
  width: 100vw;
  height: 5vh;
  background-color: rgb(255, 231, 56);
  position: fixed;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
.userBox {
  width: 100%;
  height: 4vh;
  display: flex;
  align-items: center;
}
.btn {
  border: 2px solid rgb(255, 231, 56);
  background-color: white;
  color: rgb(39, 0, 112);
  font-size: 100%;
  cursor: pointer;
}
.kakao {
  border-color: rgb(255, 231, 56);
  color: rgb(39, 0, 112);
}
.kakao:hover {
  background: rgb(255, 231, 56);
}
</style>
