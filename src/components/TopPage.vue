<template>
  <div class="main">
    <div class="header">
      <div class="first-rack">
        <button class="edit" @click="openEditModal">抽選項目編集</button><br>
      </div>
      <!-- <div class="second-rack">
        <button class="list" @click="openListModal">保存済み項目</button>
        <button class="add" @click="addList">抽選項目保存</button>
      </div> -->
    </div>

    <div class="pointer">▼</div>

    <div class="circle">
      <PieChart :chartData="pieData" :options="options" :plugins="plugins" id="roulette"/>
    </div>

    <div class="footer">
      <button class="start" @click="startRotating">スタート</button>
      <button class="reset" @click="reset">リセット</button>
    </div>
  </div>

  <EditModal @close="closeEditModal" v-if="editModal" v-on:reflect="reflectList"/>
  <SavedListModal @close="closeListModal" v-if="listModal" :saved-list="savedList"/>
</template>

<script>
import EditModal from './EditModal.vue'
import SavedListModal from './SavedListModal.vue'
import { Chart, registerables } from "chart.js";
import { PieChart } from "vue-chart-3";
import ChartDataLabels from 'chartjs-plugin-datalabels';

Chart.register(...registerables);

const backgroundColor = [
      "rgb(255, 99, 132)",
      "rgb(54, 162, 235)",
      "rgb(255, 205, 86)",
      "rgb(255, 99, 132)",
      "rgb(54, 162, 235)",
      "rgb(255, 205, 86)",
      "rgb(255, 99, 132)",
      "rgb(54, 162, 235)",
      "rgb(255, 205, 86)",
      "rgb(255, 99, 132)",
      "rgb(54, 162, 235)",
      "rgb(255, 205, 86)",
      "rgb(255, 99, 132)",
      "rgb(54, 162, 235)",
      "rgb(255, 205, 86)",
];

const listName = [];
const labelColor = [];
const splitRatio = [];

export default {
  components: {
    EditModal,
    SavedListModal,
    PieChart,
  },
  computed: {
    pieData() {
      if (this.list.length === 0) {
        return this.defalutPie;
      } else if (this.list.length) {
        listName.length = 0;
        labelColor.length = 0;
        splitRatio.length = 0;        

        for (var key in this.list) {
          listName.push(this.list[key].name);
          labelColor.push(backgroundColor[key]);
        }

        const culcArray = this.culcSplitRatio(listName.length);
        for (var r in culcArray) {
          splitRatio.push(culcArray[r]);
        }

        return this.editListPie;
      }
      return this.defalutPie;
    },
    options() {
      return {
        plugins: {
          tooltip: {
            enabled: false
          },
          legend: {
            labels: {
              fontColor: 'black'
            },
            display: false
          },
          datalabels: {
            color: 'black',
            anchor: 'center',
            font: {
              weight: "bold",
              size: 25
            },
            formatter: (value, element) => {
              let label = element.chart.data.labels[element.dataIndex];
              return label;
            }
          }
        },
        animation: false
      }
    },
    plugins() {
      return [
        ChartDataLabels
      ]
    },
    defalutPie() {
      return {
        labels: ["","","","",""],
        datasets: [
          {
            data: [100,100,100,100,100],
            backgroundColor: [
              "rgb(255, 99, 132)",
              "rgb(54, 162, 235)",
              "rgb(255, 205, 86)",
              "rgb(255, 99, 132)",
              "rgb(54, 162, 235)"
            ]
          }
        ]
      }
    },
    editListPie() {
      return {
        labels: listName,
        datasets: [
          {
            data: splitRatio,
            backgroundColor: labelColor
          }
        ]
      }
    }
  },
  data() {
    return {
      editModal: false,
      listModal: false,
      list: []
    }
  },
  props: [
    'savedList'
  ],
  emits: ['add'],
  methods: {
    openEditModal: function() {
      this.editModal = true;
    },
    closeEditModal: function() {
      this.editModal = false;
    },
    openListModal: function() {
      this.listModal = true;
    },
    closeListModal: function() {
      this.listModal = false;
    },
    reflectList: function(newList) {
      this.list = newList;
      this.editModal = false;
      return
    },
    addList: function() {
      this.$emit('add', this.list);
      this.list = [];
      alert('抽選項目を保存しました。');
    },
    startRotating: function() {
      const duration = 8000;
      const initialDegree = 0;
      const finalDegree = 3600 + 7 * Math.floor( Math.random() * 361);
      return new Promise((resolve) => {
        const keyFrames = new KeyframeEffect(
          document.getElementById('roulette'),
          [
            {transform: `rotate(${initialDegree}deg)`}, // 初期値
            {transform: `rotate(${finalDegree}deg)`} // 回転後の角度
          ],
          {
            duration: duration, // 回り終えるまでの時間
            easing: 'ease-in-out', // 回転の種類
            iterations: 1, // 動作の繰り返す数
            fill: 'forwards' // 終了時の動作
          }
        );
        const playAnimation = new Animation(keyFrames);
        playAnimation.play();
        playAnimation.onfinish = () => {resolve()};
      })
    },
    culcSplitRatio: function(length) {
      const array = [];
      for (let count = 0; count < length; count++) {
        array.push(100 / length);
      }
      return array;
    },
    reset: function() {
      listName.length = 0;
      labelColor.length = 0;
      splitRatio.length = 0;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.first-rack {
  margin: 20px;
}
.second-rack {
  margin: 20px;
  text-align: center;
}
button {
  margin: 0 10px;
  display: inline-block;
  padding: 1rem 2rem;
  border: none;
  border-radius: 0.25em;
  cursor: pointer;
}
.pointer {
  text-align: center;
  color: red;
  font-size: xx-large;
  font-weight: bold;
}
.circle {
  height: 400px;
  width: 400px;
  margin: 0 auto;
  display: flex;
}
.start {
  margin: 30px;
}

@keyframes rotate-anime {
  0%    { transform: rotate(0); }
  100%  { transform: rotate(360deg); }
}
</style>
