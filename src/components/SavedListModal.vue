<template>
  <transition name="modal" appear>
    <div class="modal modal-overlay" @click.self="$emit('close')">
      <div class="modal-window">
        <div class="modal-content">
          <table>
            <thead>
              <tr>
                <th colspan="7" class="list"></th>
                <th colspan="2" class="action"></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="list in savedList" v-bind:key="list.id">  
                <td colspan="7" class="box">
                  <div v-for="item in list.column" v-bind:key="item.id">{{ item.name }}</div>
                </td>
                <td colspan="2">
                  <button class="reflect" @click="useList(list)">反映</button><br />
                  <button class="delete" @click="deleteList(list)">削除</button>
                </td>
              </tr>
            </tbody>
          </table>
          <slot/>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {

  props: [
    'savedList'
  ],
  methods: {
    useList: function(list) {
      this.$emit('use', list);
    },
    deleteList: function(list) {
      this.$emit('delete', list);
    }
  }
}
</script>


<style lang="stylus" scoped>
.modal {
  &.modal-overlay {
    display: flex;
    align-items: center;
    justify-content: center;
    position: fixed;
    z-index: 30;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
  }

  &-window {
    background: #fff;
    border-radius: 4px;
    overflow: hidden;
    width: 40%;
    height: 60%;
  }

  &-content {
    padding: 50px 20px;
  }
}

// オーバーレイのトランジション
.modal-enter-active, .modal-leave-active {
  transition: opacity 0.4s;

  // オーバーレイに包含されているモーダルウィンドウのトランジション
  .modal-window {
    transition: opacity 0.4s, transform 0.4s;
  }
}

// ディレイを付けるとモーダルウィンドウが消えた後にオーバーレイが消える
.modal-leave-active {
  transition: opacity 0.6s ease 0.4s;
}

.modal-enter, .modal-leave-to {
  opacity: 0;

  .modal-window {
    opacity: 0;
    transform: translateY(-20px);
  }
}

button {
  padding: 0 8px;
  margin: 2px;
  line-height: 24px;
  border: 2px solid #6091d3;
  border-radius: 3px;
  cursor: pointer;
  color: #6091d3;
  background-color: #fff;
  font-weight: bold;
  cursor: pointer;
}

table {
  width: 80%;
  margin: 0 auto;
  border-collapse: collapse;
}

.box {
  width: 87%;
  font-weight: bold;
  color: #6091d3;
  background: #FFF;
  border: solid 2px #6091d3;
  border-radius: 3px;
}
</style>