<!-- pages/index.vue -->
<template>
  <div>
    <section class="container" v-if="$store.state.auth">
      <h1>Todoリスト</h1>
      <div class="addArea">
        <input type="text" name="addName" id="addName" v-model='content' placeholder="タスクを入力してください">
        <button id="addButton" class="button button--green" @click='insert'>追加</button>
      </div>
      <div class="Filter">
        <button class="button button--gray is-active" :class="{'is-active': (!find_flg)}" @click='flag_reset'>全て</button>
        <button class="button button--gray" :class="{'is-active':find_flg && (find_state == '作業前')}" @click='find("作業前")'>作業前</button>
        <button class="button button--gray" :class="{'is-active':find_flg && (find_state == '作業中')}" @click='find("作業中")'>作業中</button>
        <button class="button button--gray" :class="{'is-active':find_flg && (find_state == '完了')}" @click='find("完了")'>完了</button>
      </div>
      <table class="Lists">
        <thead>
          <tr>
            <th>タスク</th>
            <th>登録日時</th>
            <th>状態</th>
            <th> </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in display_todos" :key="index">
            <td>{{ item.content }}</td>
            <td>{{ item.created }}</td>
            <td><button class="button"
                        v-bind:class="{
                          'button--yet':item.state == '作業前',
                          'button--progress':item.state == '作業中',
                          'button--done':item.state == '完了'}"
                          @click="changeState(item)">
                        {{ item.state }}
                </button></td>
            <td><button class="button button--red" @click="remove(item)">削除</button></td>
          </tr>
        </tbody>
      </table>
    </section>
    <div v-else>
      Please
      <NuxtLink to='/login'>
        login
      </NuxtLink>
    </div>
  </div>
</template>

<script>
import {mapState} from 'vuex';

export default {
  data: function() {
    return {
      content: '',
      find_state: '',
      find_flg: false
    }
  },
  computed: {
    ...mapState(['todos']),
    display_todos: function() {
      if(this.find_flg) {
        let arr = [];
        let data = this.todos;
        data.forEach(element => {
          if(element.state == this.find_state) {
            arr.push(element);
          }
        });
        return arr;
      } else {
        return this.todos;
      }
    }
  },
  methods: {
    insert: function() {
      if(this.content != ''){
        this.$store.commit('insert', {content: this.content});
        this.content = '';
      }
    },
    remove: function(todo) {
      this.$store.commit('remove', todo)
    },
    changeState: function(todo){
      this.$store.commit('changeState', todo)
    },
    find: function(findState) {
      this.find_state = findState;
      this.find_flg = true;
    },
    flag_reset: function() {
      this.find_flg = false;
    }
  }
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family:
    'Quicksand',
    'Source Sans Pro',
    -apple-system,
    BlinkMacSystemFont,
    'Segoe UI',
    Roboto,
    'Helvetica Neue',
    Arial,
    sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
