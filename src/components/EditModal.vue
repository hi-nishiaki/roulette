<template>
  <transition name="modal" appear>
    <div class="modal modal-overlay" @click.self="$emit('close')">
      <div class="modal-window">
        <div class="modal-content">
          <button class="add" @click="addColumn">追加</button>
          <button class="update" @click="reflectList">確定</button>

          <div class="list">
            <table>
              <thead>
                <tr>
                  <th colspan="5" class="name"></th>
                  <th colspan="2" class="delete"></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="col in inputList" v-bind:key="col.id">
                  <td colspan="5">
                    <input type="text" v-model="col.name">
                  </td>
                  <td colspan="2">
                    <button class="delete" @click="deleteColumn(col)">削除</button>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <slot/>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "editModal",
  data() {
    return {
      inputList: [],
      newList: {
        name: ""
      }
    }
  },
  emits: ['reflect'],
  methods: {
    addColumn: function() {
      if (this.inputList.length >= 10) {
        alert('登録は10項目までです。');
        return;
      }
      this.inputList.push({
        id: this.inputList.length,
        name: "",

      });
    },
    deleteColumn: function(col) {
      var index = this.inputList.indexOf(col);
      this.inputList.splice(index, 1);
    },
    reflectList: function() {
      this.$emit('reflect', this.inputList);
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
  margin: 0 10px;
  display: inline-block;
  padding: 0.5rem 2rem;
  border: 1px solid;
  border-radius: 5px;
  cursor: pointer;
  background-color: #fff;
}

.add {
  color: #66CDAA;
  border-color: #66CDAA;
}

.update {
  color: #FA8072;
  border-color: #FA8072;
}

table {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 30px; 10px;
}

.delete {
  margin: 5px auto;
  padding: 3px 5px;
  color: #A9A9A9;
}

input[type=text] {
  margin: 5px auto;
  padding: 5px;
  border: 1px solid #A9A9A9;
  border-radius: 8px;
}
</style>