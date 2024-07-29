<template>
  <div>
    <div class="container">
      <button class="button" @click="createContact = !createContact">Добавить</button>
    </div>
    <div class="container">
      <div class="user-list">
        <div class="table">
          <div class="row header">
              <div class="cell">Имя</div>
              <div class="cell phone">Телефон</div>
          </div>
          <div class="row" v-for="(column, index) in flattenedUsers" :key="index">
              <div class="cell" :style="{ 'margin-left': column.level * 20 + 'px' }">{{ column.name }}</div>
              <div class="cell phone">{{ column.phone }}</div>
          </div>
        </div>
      </div>
      <div v-if="createContact" class="modal">
        <div class="modal-header">
          <div>Добавление пользователя</div>
          <div @click="createContact = false" class="close-btn">X</div>
        </div>
        <div class="modal-content">
          <div class="modal-content__stroke __margin-top-10">
            <div>Имя</div>
            <input class="input" v-model="name">
          </div>
          <div class="modal-content__stroke __margin-top-10">
            <div>Номер</div>
            <input class="input" v-model="phone">
          </div>
          <div class="modal-content__stroke __margin-top-10">
            <div>Начальник</div>
            <select class="select" v-model="parentIndex">
              <option value="">-</option>
              <option v-for="(user, index) in flattenedUsers" :key="index" :value="user.id">
                {{ '+'.repeat(user.level) + ' ' + user.name }}
              </option>
            </select>
          </div>
          <div class="__margin-top-10">
            <button class="button" @click="addData">Сохранить</button>
          </div>
        </div>
      </div>
    </div>
  </div>
  
</template>

<style>

.__margin-top-10 {
  margin-top: 10px;
}

.button {
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    color: white;
    transition: 0.2s linear;
    background-color: gray;
    outline: none;
    cursor: pointer;
  }

  .button:hover {
    box-shadow: 0 0 0 2px white, 0 0 0 4px #3C82F8;
  }

  .button:active {
    box-shadow: none;
  }

  .input {
    max-width: 200px;
    width: 100%;
    padding: 5px;
    border-radius: 5px;
    border: 1px solid #ccc;
    outline: none;
  }

  .select {
    padding: 5px;
    border-radius: 5px;
    max-width: 200px;
    width: 100%;
  }

  .table {
    display: flex;
    flex-direction: column;
  }

  .row {
    display: flex;
    flex-shrink: 1;
  }

  .header {
    font-weight: bold;
  }

  .cell {
    flex: 1;
    padding: 5px;
    border: 1px solid #ccc;
    width: 100px;
  }

  .cell.phone {
    min-width: 200px;
    max-width: 200px;
  }
  
  .container {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-between;
    max-width: 800px;
    margin: 0 auto;
    padding: 20px;
  }

  .user-list {
    display: flex;
  }

  .modal {
    padding: 20px;
    border: 1px solid #ccc;
    width: 300px;
    height: auto;
  }

  .modal-header {
    display: flex;
    justify-content: space-between;
    font-weight: bold;
  }

  .modal-content {
    margin-top: 10px;
  }

  .modal-content__stroke {
    display: flex;
    justify-content: space-between;
  }

  .close-btn {
    cursor: pointer;
  }
</style>

<script>
export default {
  name: 'App',
  data() {
    return {
      testTable: [],
      createContact: false,
      name: '',
      phone: '',
      parentIndex: '',
    };
  },
  computed: {
    flattenedUsers() {
      return this.flattenUsers(this.testTable);
    }
  },
  mounted() {
    this.loadData ();
  },
  methods: {
    flattenUsers(users, level = 0, parentId = '') {
      return users.reduce((acc, user, index) => {
        const userData = { ...user, level, parentId, id: parentId + index };
        acc.push(userData);
        if (user.children && user.children.length > 0) {
          const childrenData = this.flattenUsers(user.children, level + 1, parentId + index + '-');
          acc.push(...childrenData);
        }
        return acc;
      }, []);
    },

    addData() {
      if (this.name.length<2 && this.phone.length<3){
        
        return
      }
      if (!this.parentIndex) {
        this.testTable.push({ name: this.name, phone: this.phone, children: [] });
      } else {
        const selectedUser = this.flattenedUsers.find(user => user.id === this.parentIndex);
        if (selectedUser) {
          selectedUser.children.push({ name: this.name, phone: this.phone, children: [] });
        }
      }
      this.saveData();
      this.name = '';
      this.phone = '';
      this.parentIndex = '';
    },

    saveData() {
      localStorage.setItem('data', JSON.stringify(this.testTable));
    },

    loadData() {
    const data = localStorage.getItem('data');
    if (data) {
      this.testTable = JSON.parse(data);
    }
  }
  },

};
</script>
