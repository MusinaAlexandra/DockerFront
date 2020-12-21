<template>
  <div>
    <div class="home">
      <div id="insert_layout">
        <div id="inputT">
          <input v-model="url" type="text" placeholder="Вставьте url"/>
          <button id="button" v-on:click="saveURL()">Сохранить</button>
        </div>
        <label id="save_id" v-show="idFlag">ID сохранения: {{ saveURL }}</label>
        <label id="no_save_id" v-show="noIdFlag">  </label>
        <label id="err_save_id" v-show="errFlag">
          В процессе что-то пошло не так :( Попробуйте снова
        </label> <br><br>
      </div>
      <div id="search_layout">
        <div id="inputT">
          <input v-model="inputID" type="text" placeholder="Введите id ссылки"/>
          <button id="button" v-on:click="serchID()">Розыскать</button>
        </div>
        <label id="no_save_id">  </label>
        <label id="about_all">Вы можете получить все ссылки по запросу /all </label> <br><br>
        <label id="err_search_id" v-show="errSFlag">
          В процессе что-то пошло не так :( Попробуйте снова
        </label>
        <label id="no_save_id" v-show="!errSFlag">  </label> <br>
      </div>
      <div id="IDanswer" v-show="idIsFound">
        <div id="table">
          <div id="columns">
            <div id="columnID" class="columnTitle row-field">ID</div>
            <div id="columnURL" class="columnTitle row-field">URL</div>
            <div id="columnStatus" class="columnTitle row-field">Статус</div>
          </div>
          <div id="rows">
            <div class="row-field">
              {{ foundURL.id }}
            </div>
            <div class="row-field">
              {{ foundURL.url }}
            </div>
            <div id="columnStatus" class="row-field">
              {{ foundURL.status }}
            </div>
          </div>
        </div>
      </div>
      <div id="Listanswer" v-show="listIsFound">
        <div id="table">
          <div id="columns">
            <div id="columnID" class="columnTitle row-field">ID</div>
            <div id="columnURL" class="columnTitle row-field">URL</div>
            <div id="columnStatus" class="columnTitle row-field">Статус</div>
          </div>
          <div id="rows">
            <div
              v-for="currURL in foundListURL"
              v-bind:key="currURL.id"
              class="row"
            >
              <div class="row-field">
                {{ currURL.id }}
              </div>
              <div class="row-field">
                {{ currURL.url }}
              </div>
              <div class="row-field" id="columnStatus">
                {{ currURL.status }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import axios from 'axios';
import Link from '../models/Link';

@Component
export default class Home extends Vue {
  idFlag = false;

  noIdFlag = true;

  errFlag = false;

  saveID = -1;

  url = '';

  saveURL() {
    if (this.url === '') {
      this.idFlag = false;
      this.errFlag = false;
      this.noIdFlag = true;
    } else {
      axios
        .post('http://192.168.99.100:8500/links', this.url)
        .then((response) => {
          if (response.data !== 'NOT_FOUND') {
            this.saveID = response.data;
            this.errFlag = false;
            this.noIdFlag = false;
            this.idFlag = true;
          } else {
            this.errFlag = true;
            this.noIdFlag = false;
            this.idFlag = false;
          }
        });
    }
    this.url = '';
  }

  inputID = '';

  idIsFound = false;

  listIsFound = false;

  errSFlag = false;

  foundURL = {} as Link;

  foundListURL = [] as Link[];

  serchID() {
    if (this.inputID !== '') {
      if (this.inputID.toLowerCase() === '/all') {
        this.foundURL = {} as Link;
        this.idIsFound = false;
        this.listIsFound = true;
        axios
          .get('http://192.168.99.100:8500/links')
          .then((response) => {
            if (response.data !== 'NOT_FOUND') {
              this.errSFlag = true;
            } else {
              this.foundListURL = response.data;
              this.errSFlag = false;
            }
          });
      } else {
        this.foundListURL = {} as Link[];
        this.listIsFound = false;
        const numID = parseInt(this.inputID, 10);
        if (!Number.isNaN(numID)) {
          axios
            .get(`http://192.168.99.100:8500/links/${parseInt(this.inputID, 10)}`)
            .then((response) => {
              if (response.data !== 'NOT_FOUND') {
                this.errSFlag = true;
              } else {
                this.foundURL = response.data;
                this.errSFlag = false;
              }
            });
          this.idIsFound = true;
          this.errSFlag = false;
        } else {
          this.idIsFound = false;
          this.errSFlag = true;
        }
      }
    } else {
      this.idIsFound = false;
      this.listIsFound = false;
      this.errSFlag = false;
    }
    this.inputID = '';
  }
}
</script>

<style scoped lang="scss">
::-webkit-input-placeholder {
  color: gray;
  opacity: 0.95;
}

.home {
  position: fixed;
  left: 25%;
  height: 100%;
  width: 50%;
  background-color: white;
  border-radius: 8px;
}
#insert_layout {
  width: 80%;
  margin: 10% 10% 0%;
}

#search_layout {
  width: 80%;
  margin: 0% 10%;
}

#inputT {
  display: flex;
  justify-content: space-between;
  input {
    font-size: 15pt;
  }
}

button {
  font-size: 15pt;
  width: 115px;
  border: 1px solid black;
  background: black;
  color: white;
  padding: 10px;
  border-radius: 8px;
}

#table {
  display: grid;
  width: 90%;
  margin: 20px auto;
  .columnTitle {
    color: black;
  }
  #columns {
    display: grid;
    grid-template-columns: 1fr 3fr 1fr;
    grid-template-rows: minmax(30px, auto);
    border-bottom: 1px solid black;
  }
  .row {
    display: grid;
    grid-template-columns: 1fr 3fr 1fr;
    grid-template-rows: minmax(35px, auto);
    border-top: 1px solid black;
    vertical-align: middle;
  }
  .row-field {
    display: flex;
    text-align: center;
    opacity: 95%;
    justify-content: center;
    align-items: center;
    font-size: 10pt;
    border-right: 1px solid black;
  }

  #columnStatus {
    border-right: 1px solid white;
  }
}
</style>
