<template>
  <div id="roomWrapper">
    <div id="enterInfo">
      입장 정보 | ROOM-ID : {{ roomId }} | USER-ID : {{ userId }}
    </div>
    <div id="chat">
      <div
        v-for="item in spreadArray"
        :key="item.spreadInfoKey"
        class="spreadWrapper"
      >
        <div class="spreadTitle">
          뿌리기 정보
        </div>
        <div class="spreadInfo">
          뿌린사람 : {{ item.userId }}
          <br />
          <br />
          총 금액 : {{ item.totalAmount | currency }}
          <br />
          <br />
          총 인원 : {{ item.totalUserCount | currency }}
          <br />
          <br />
          뿌리기 시간 : {{ item.spreadTime }}
        </div>
        <div class="spreadBtn">
          <button class="btn" @click="receive(item.token)">받기</button>
          <p class="btnSpace"></p>
          <button class="btn" @click="inquiry(item.token)">조회</button>
        </div>
      </div>
    </div>
    <div id="createWrapper" @click="createModal">뿌리기 생성</div>
    <SpreadCreateModal
      v-if="spreadModalBool"
      v-bind:userId="userId"
      v-bind:roomId="roomId"
      @close="closeModal"
      @create="createEndModal"
    >
    </SpreadCreateModal>
    <ReceiveModal v-if="receiveModalBool" @close="closeModal">
      <div slot="body" class="receiveBox">
        받은 금액 : {{ receiveData.receiveAmount | currency }}
      </div>
      <div slot="body" class="receiveBox">
        받은 시간 :
        {{ moment(receiveData.receiveTime).format("YYYY-MM-DD HH:mm:ss") }}
      </div>
    </ReceiveModal>
    <InquiryModal v-if="inquiryModalBool" @close="closeModal">
      <div slot="body" class="inquiryTitle">
        총 뿌린 금액 : {{ spreadInquiryArray.totalSpreadAmount | currency }}
      </div>
      <div slot="body" class="inquiryTitle">
        받아갈수 있는 총 인원수 :
        {{ spreadInquiryArray.totalSpreadUserCount | currency }}
      </div>
      <div slot="body" class="inquiryTitle">
        총 받아간 금액 : {{ spreadInquiryArray.totalReceiveAmount | currency }}
      </div>
      <div slot="body" class="inquiryTitle">
        받아간 총 인원수 :
        {{ spreadInquiryArray.totalReceiveUserCount | currency }}
      </div>
      <div slot="body" class="inquiryTitle">내 역</div>
      <div
        slot="body"
        class="inquiryList"
        v-for="(item, index) in spreadInquiryArray.srInfoList"
        :key="item.userId"
      >
        <div class="listBox">유저 : {{ item.userId }}</div>
        <div class="listBox">금액 : {{ item.receiveAmount | currency }}</div>
        <div
          class="listBox"
          :class="{
            lastBorder: index == spreadInquiryArray.srInfoList.length - 1,
          }"
        >
          시간 : {{ moment(item.receiveTime).format("YYYY-MM-DD HH:mm:ss") }}
        </div>
      </div>
    </InquiryModal>
  </div>
</template>
<script>
import axios from "axios";
import SpreadCreateModal from "@/components/SpreadCreateModal.vue";
import ReceiveModal from "@/components/ReceiveModal.vue";
import InquiryModal from "@/components/InquiryModal.vue";
export default {
  name: "Home",
  props: {
    roomUserInfoKey: Number,
    userId: Number,
    roomId: String,
  },
  components: {
    SpreadCreateModal,
    ReceiveModal,
    InquiryModal,
  },
  data: function () {
    return {
      spreadModalBool: false,
      spreadArray: [],
      receiveModalBool: false,
      receiveData: {},
      inquiryModalBool: false,
      spreadInquiryArray: [],
    };
  },
  filters: {
    currency: function (value) {
      var num = new Number(value);
      return num.toFixed(0).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,");
    },
  },
  mounted() {
    this.roomSpreadInfoList(this.roomId);
  },
  methods: {
    roomSpreadInfoList(roomId) {
      let url = `/api/v2/roomSpreadInfoList/${roomId}`;
      axios
        .get(url)
        .then((res) => {
          this.spreadArray = res.data;
          console.log(this.spreadArray);
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
    receive(token) {
      console.log(token);
      let url = `/api/v1/spread/${token}`;
      let data = {};
      let config = {
        headers: {
          "X-USER-ID": this.userId,
          "X-ROOM-ID": this.roomId,
        },
      };
      axios
        .put(url, data, config)
        .then((res) => {
          this.receiveData = res.data;
          this.receiveModalBool = true;
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
    inquiry(token) {
      let url = `/api/v1/spread/${token}`;
      let config = {
        headers: {
          "X-USER-ID": this.userId,
          "X-ROOM-ID": this.roomId,
        },
      };
      axios
        .get(url, config)
        .then((res) => {
          this.spreadInquiryArray = res.data;
          this.inquiryModalBool = true;
          console.log(this.spreadInquiryArray);
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
    createModal() {
      this.spreadModalBool = true;
    },
    closeModal() {
      this.spreadModalBool = false;
      this.receiveModalBool = false;
      this.inquiryModalBool = false;
    },
    createEndModal() {
      this.spreadModalBool = false;
      this.roomSpreadInfoList(this.roomId);
    },
  },
};
</script>
<style scoped>
#roomWrapper {
  width: 100vw;
  height: 100%;
  min-height: 100vh;
  box-sizing: border-box;
  padding-top: 8vh;
  margin-bottom: 7vh;
  background-color: rgb(179, 203, 214);
}
#enterInfo {
  position: fixed;
  top: 0;
  width: 100vw;
  height: 8vh;
  display: flex;
  align-items: center;
  padding: 1vh 2vw;
  box-sizing: border-box;
  background-color: rgb(255, 255, 255);
}
#chat {
  width: 100vw;
  height: 100%;
  box-sizing: border-box;
}
#createWrapper {
  position: fixed;
  bottom: 0;
  width: 100vw;
  height: 7vh;
  box-sizing: border-box;
  background-color: rgb(255, 231, 56);
  display: flex;
  justify-content: center;
  align-items: center;
}
.spreadWrapper {
  width: 100vw;
  height: 33vh;
  box-sizing: border-box;
  padding: 2vh 3vw;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.spreadTitle {
  width: 90%;
  height: 30%;
  background-color: rgb(255, 231, 56);
  display: flex;
  box-sizing: border-box;
  padding: 10px;
  font-size: 140%;
  font-weight: bold;
}
.spreadInfo {
  width: 90%;
  height: 50%;
  background-color: rgb(255, 255, 255);
  display: flex;
  box-sizing: border-box;
  padding: 10px;
  font-size: 90%;
  font-weight: bold;
}
.spreadBtn {
  width: 90%;
  height: 20%;
  background-color: rgb(255, 255, 255);
  display: flex;
  box-sizing: border-box;
  padding: 10px;
  font-size: 100%;
  justify-content: center;
  align-items: center;
  border-top: 1px solid rgb(190, 190, 190);
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
.btnSpace {
  width: 20%;
}
.receiveBox {
  width: 100%;
  height: 4vh;
  display: flex;
  align-items: center;
}
.inquiryTitle {
  width: 100%;
  height: 3vh;
  line-height: 0.5vh;
  font-weight: bold;
}
.inquiryList {
  width: 100%;
  border-top: 1px solid black;
  border-left: 1px solid black;
  border-right: 1px solid black;
  align-items: center;
}
.listBox {
  width: 100%;
}
.lastBorder {
  border-bottom: 1px solid black;
}
</style>
