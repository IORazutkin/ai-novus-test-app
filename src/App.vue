<template>
  <div id="app">
    <sorted-list :items="taskList" />
  </div>
</template>

<script>
import SortedList from './components/SortedList'

export default {
  name: 'App',
  components: { SortedList },
  data () {
    return {
      taskList: [
        {
          id: 1,
          message: 'Купить молока и Nesquik'
        }, {
          id: 2,
          message: 'Сдать тестовое задание в Ай-Новус'
        }, {
          id: 3,
          message: 'Разобраться с DOM-событиями drag и drop'
        }, {
          id: 4,
          message: 'Научиться выводить линию между этими задачами по наведению draggable объекта (опционально)'
        }
      ]
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
</style>
