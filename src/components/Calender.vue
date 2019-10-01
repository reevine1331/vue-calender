<template>
  <div class="calender">
    <div class="calender-component">
      <div class="calender-header">
        <div class="arrow-back" v-on:click="changeMonth(0)"><</div>
        <div class="current-date">{{ dateLabel }}</div>
        <div class="arrow-next" v-on:click="changeMonth(1)">></div>
      </div>
      <div class="calender-body">
        <ul class="calender-panel-list">
          <li class="calender-panel_space" v-for="space in spaces"></li>
          <li
            class="calender-panel"
            v-on:click="selectDate"
            v-for="date in dates"
            :id="date.date"
            v-bind:class="selectedDate === date.date ? 'selected' : ''"
          >
            <div class="calender-date">{{ date.dateNumber }}</div>
            <div
              class="calender-todo"
              v-bind:class="date.todoNumber !== '-' ? 'number' : ''"
            >{{ date.todoNumber }}</div>
          </li>
        </ul>
      </div>
      <div class="calender-footer">
        <div
          class="calender-footer_todo"
          v-for="todo in todoList"
          v-show="todo.date === selectedDate"
        >
          <div class="calender-footer_todo-time">{{ todo.time }}</div>
          <div class="calender-footer_todo-title">{{ todo.title }}</div>
          <div class="calender-footer_todo-description">{{ todo.description }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import moment from "moment";
import TODO_LIST from "../data/todoList";

export default {
  mixins: [TODO_LIST],
  data() {
    return {
      todoList: TODO_LIST, //todoリスト
      dates: [], //カレンダーの日付
      spaces: [], //その月の最初日が始まる場所
      dateLabel: "", //フォーマット="2019年7月"
      selectedMonth: null, //今選択している月
      selectedDate: null //今選択している日付
    };
  },
  methods: {
    selectDate(event) {
      //日付を選択する
      this.selectedDate = event.currentTarget.id;
    },
    changeMonth(num) {
      //月を変更
      if (num === 0) {
        this.selectedMonth = moment(this.selectedMonth).subtract(1, "months");
      } else {
        this.selectedMonth = moment(this.selectedMonth).add(1, "months");
      }
    }
  },
  created() {
    //画面表示時に今日の日付と月を設定。
    this.selectedDate = moment().format("YYYY-MM-DD");
    this.selectedMonth = moment();
  },
  watch: {
    selectedMonth: function() {
      //選択している月の変更時の処理、画面表示時も動きます
      this.dateLabel = moment(this.selectedMonth).format("YYYY年MM月"); //月ラベルを更新
      this.spaces = []; //スペースを初期化
      for (
        let i = 0;
        i <
        moment(this.selectedMonth)
          .startOf("month")
          .day();
        i++
      ) {
        //スペースを更新
        this.spaces[i] = i;
      }

      this.dates = []; //カレンダーパネルを初期化
      for (let i = 0; i < moment(this.selectedMonth).daysInMonth(); i++) {
        //カレンダーパネルを更新
        let todoNumber = "-";
        for (let k of Object.keys(this.todoList)) {
          //todoListの情報をカレンダーパネルに追加
          if (this.dates[i]) {
            if (this.todoList[k].date === this.dates[i].date) {
              todoNumber++;
            }
          }
        }
        this.dates[i] = {
          date: moment(this.selectedMonth)
            .startOf("month")
            .add(i, "day")
            .format("YYYY-MM-DD"),
          dateNumber: i + 1,
          todoNumber: todoNumber
        };
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.calender {
  width: 336px;
}

.calender-component {
  min-height: 320px;
  border: solid 1px gray;
  padding: 24px;
  box-sizing: border-box;
}

.calender-header {
  display: flex;
  justify-content: space-between;
}

.arrow-back {
  font-size: 16px;
  color: gray;
  user-select: none;
  cursor: pointer;
  text-align: center;
  width: 24px;
  height: 24px;
}

.arrow-back:hover {
  background-color: silver;
  border-radius: 4px;
}

.arrow-next {
  font-size: 16px;
  color: gray;
  user-select: none;
  cursor: pointer;
  text-align: center;
  width: 24px;
  height: 24px;
}

.arrow-next:hover {
  background-color: silver;
  border-radius: 4px;
}

.current-date {
  color: gray;
  user-select: none;
}

.calender-body {
  margin-top: 24px;
}

.calender-panel-list {
  display: flex;
  flex-wrap: wrap;
  padding: 0;
  justify-content: left;
}

.calender-panel {
  padding: 8px 0px;
  width: 40px;
  text-align: center;
  color: gray;
  font-size: 14px;
  user-select: none;
  list-style: none;
}

.calender-panel:hover {
  background-color: silver;
  border-radius: 4px;
}

.calender-date {
  cursor: pointer;
}

.selected {
  background-color: silver;
  border-radius: 4px;
}

.calender-todo {
  cursor: pointer;
  text-align: center;
  margin: 0px 8px;
  line-height: 25px;
}

.calender-panel_space {
  width: 40px;
  list-style: none;
}

.calender-footer_todo {
  border-top: 1px gray solid;
  padding-top: 12px;
  margin-top: 8px;
}

.calender-footer_todo-time {
  font-size: 18px;
  color: gray;
  word-break: break-word;
  text-decoration: underline;
}

.calender-footer_todo-title {
  font-size: 20px;
  color: gray;
  word-break: break-word;
}

.calender-footer_todo-description {
  font-size: 14px;
  color: gray;
  word-break: break-word;
}

.number {
  text-decoration: underline;
}
</style>
