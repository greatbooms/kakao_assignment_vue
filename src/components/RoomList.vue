<template>
  <div id="roomBox">
    <div id="countImg">
      <img
        v-if="peopleCount < 3"
        alt="people logo"
        src="../assets/count2.png"
      />
      <img
        v-else-if="peopleCount === 3"
        alt="people logo"
        src="../assets/count3.png"
      />
      <img
        v-else-if="peopleCount === 4"
        alt="people logo"
        src="../assets/count4.png"
      />
      <img v-else alt="people logo" src="../assets/countMore.png" />
    </div>
    <div id="roomTotalInfo">
      <div id="roomTitle">
        <div id="roomName">{{ roomId }}</div>
        <div id="roomPeopleCount">{{ peopleCount | currency }}</div>
      </div>
      <div id="roomLatestMsg"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: "RoomList",
  props: {
    roomId: String,
    peopleCount: Number,
  },
  filters: {
    currency: function (value) {
      var num = new Number(value);
      return num.toFixed(0).replace(/(\d)(?=(\d{3})+(?:\.\d+)?$)/g, "$1,");
    },
  },
};
</script>

<style scoped>
#roomBox {
  width: 96vw;
  height: 9vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 1vh 2vw;
}
#countImg {
  display: inline-flex;
  width: 20%;
  height: 100%;
  justify-content: center;
  align-items: center;
}
#roomTotalInfo {
  width: 80%;
  height: 100%;
  display: inline-flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
#roomTitle {
  width: 95%;
  height: 40%;
  display: flex;
  align-items: center;
  padding-left: 5%;
}
#roomLatestMsg {
  width: 95%;
  height: 60%;
  display: flex;
  align-items: center;
  padding-left: 5%;
}
#roomName {
  font-weight: bold;
}
#roomPeopleCount {
  padding-left: 2%;
  font-weight: 500;
  color: rgb(173, 173, 173);
  font-size: 90%;
}
img {
  width: 90%;
  height: 90%;
}
</style>
