<template>
  <div id="app">
    <div class="container">
      <div class="title-container">
        <div class="title">Leaderboard.</div>
        <input
          placeholder="Search"
          v-model="searchModel"
          class="search-input"
        />
      </div>
      <div class="loading-container" v-if="loadingState">
        <img src="@/assets/batman-thinking.gif" alt="loading" width="" />
        <div class="bouce-loading">
          <div></div>
          <div></div>
          <div></div>
        </div>
        <div class="text-white text-center tracking-widest">Loading...</div>
      </div>
      <div v-else class="overflow-x-auto">
        <div v-if="filterModels.length <= 0">No leaderboard.</div>
        <table class="table-w">
          <thead>
            <tr class="border-gray-200 bg-gray-100 border-2">
              <th
                @click="sortAction = !sortAction"
                class="py-4 w-20 rounded-left number-col"
              >
                <span class="number-txt">No.</span>
                <div
                  :class="sortAction ? 'triangle-up' : 'triangle-down'"
                ></div>
              </th>
              <th class="py-4">Model Name</th>
              <th class="py-4">Endpoint</th>
              <th
                @click="sortAction = !sortAction"
                class="py-4 rounded-right number-col"
              >
                <span class="number-txt">Count</span>
                <div
                  :class="sortAction ? 'triangle-up' : 'triangle-down'"
                ></div>
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(leader, index) in filterModels"
              class="border-gray-200 border-b bg-white col-hover"
              :key="index"
            >
              <td class="py-3 number">
                <span v-if="sortAction">
                  {{ startIndex - index }}
                </span>
                <span v-else>
                  {{ startIndex + index }}
                </span>
              </td>

              <td class="py-3 px-4 model-name scroller">
                {{ leader.modelName }}
              </td>
              <td class="py-3 px-4 scroller endpoint">
                {{ leader.endpoint }}
              </td>
              <td class="py-3 px-4 count scroller w-14">
                {{ leader.count }}
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "App",
  async created() {
    this.loadingState = true;
    const { data } = await axios.get("https://api-leaderboard.visai.ai/");

    this.leaderboardList = data.leaderboard.sort((a, b) => {
      return b.count - a.count;
    });
    this.filterModels = this.leaderboardList;
    this.loadingState = false;
  },
  data() {
    return {
      leaderboardList: [],
      searchModel: "",
      filterModels: [],
      sortAction: false,
      startIndex: 1,
      loadingState: false,
      sortedLeaderboardList: [],
    };
  },
  watch: {
    searchModel(val) {
      let tempModels = [...this.leaderboardList];
      if (val.trim() !== "") {
        this.filterModels = tempModels.filter((d) => {
          return d.modelName.toLowerCase().includes(val.toLowerCase().trim());
        });
      } else {
        this.filterModels = this.leaderboardList;
      }
    },
    sortAction(val) {
      if (val) {
        const tempLeaders = [...this.leaderboardList];
        this.filterModels = tempLeaders.reverse();
        this.startIndex = this.filterModels.length;
      } else {
        this.filterModels = this.leaderboardList;
        this.startIndex = 1;
      }
    },
  },
};
</script>

<style>
html {
  height: 100%;
  background: #363239;
}
.title {
  color: #ffffff;
  text-align: left;
  font-size: 32px;
  font-weight: 700;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  /* color: #ffffff; */
  margin-top: 50px;
}
.search-input {
  padding-left: 1rem;
  padding-right: 1rem;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  width: 50%;
  border-radius: 10px;
  background: #fcfcfc;
  font-size: 14px;
  border: 1px solid #fcfcfc;
}
.title-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}
.w-14 {
  width: 8rem;
}
.count {
  font-weight: 700;
}
.container {
  /* padding-top: 2rem; */
  padding-bottom: 2rem;
  padding-left: 2rem;
  scroll-padding-right: 2rem;
  width: 50%;
  margin: auto;
}
.table-w {
  border-radius: 6px;
  background: #eaeaea;
  width: 100%;
  min-width: 320px;
  border-collapse: collapse;
  overflow: hidden;
}
.rounded-left {
  border-top-left-radius: 6px;
}
.rounded-right {
  border-top-right-radius: 6px;
}
.overflow-x-auto {
  overflow-x: auto;
}
th {
  color: #2d2e30;
}
td {
  /* border-top-width: 0.5px;
  border-bottom-width: 0.5px;
  border-style: solid none; */
  /* max-width: 14rem; */
  border-left: 1px solid #f1f1f1;
}
.model-name {
  font-weight: 700;
  overflow-x: auto;
}

.border-2 {
  border-width: 1px;
  border-radius: 6px;
}

.border-b-2 {
  border-bottom-width: 1px;
}

.border-gray-200 {
  border-color: #b5b5b5;
}
.bg-white {
  background: #ffffff;
}

.col-hover:hover {
  background: #f2f5f7;
}

.bg-gray-100 {
  background: #f2f5f7;
}
.py-3 {
  padding-top: 0.8rem;
  padding-bottom: 0.8rem;
}
.py-4 {
  padding-top: 1rem;
  padding-bottom: 1rem;
  padding-left: 0.5rem;
  padding-right: 0.5rem;
}

.px-4 {
  padding-left: 1rem;
  padding-right: 1rem;
}

@media only screen and (max-width: 1400px) {
  .container {
    width: 70%;
  }
}

@media only screen and (max-width: 880px) {
  .container {
    width: 90%;
  }
}

::-webkit-scrollbar {
  height: 2px;
}

.scroller {
  scrollbar-width: none;
}

::-webkit-scrollbar-track {
  -webkit-border-radius: 20px;
  border-radius: 20px;
  background: transparent;
}

::-webkit-scrollbar-thumb {
  -webkit-border-radius: 20px;
  border-radius: 20px;
  background: transparent;
}

.scroller:hover::-webkit-scrollbar-thumb {
  background: #6d6d6d;
}

.scroller:hover::-webkit-scrollbar-track {
  background-color: #ebebeb;
}

.endpoint {
  font-size: 14px;
}

.number {
  font-size: 14px;
}

.number-col {
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
}
.number-col:hover {
  background: #eaecee;
}

.number-col:hover .number-txt {
  text-decoration: underline;
}

.triangle-down {
  width: 0;
  height: 0;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 10px solid #555;
  margin-left: 0.2rem;
}

.triangle-up {
  width: 0;
  height: 0;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-bottom: 10px solid #555;
  margin-left: 0.2rem;
}

.bouce-loading {
  display: flex;
  justify-content: center;
  margin-top: 1.5rem;
}

.loading-container {
  margin-top: 5rem;
  margin-bottom: 5rem;
  color: #ffffff;
}

.bouce-loading div {
  background: #eaecee;
  border-radius: 50%;
  width: 2rem;
  height: 2rem;
  margin: 0.5rem;
  animation: 0.3s bounce infinite alternate;
}

.bouce-loading div:nth-child(2) {
  animation-delay: 0.1s;
}
.bouce-loading div:nth-child(3) {
  animation-delay: 0.2s;
}

@keyframes bounce {
  to {
    opacity: 0.3;
    transform: translate3d(0, -1rem, 0);
  }
}
</style>
