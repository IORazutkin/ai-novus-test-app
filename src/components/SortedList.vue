<template>
  <div class="list-container">
    <ul class="list">
      <transition-group name="list" tag="li">
        <li v-for="(item, index) of items" class="list__item" draggable="true" :key="item.id" :data-index="index">
          {{ item.message }}
        </li>
      </transition-group>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'SortedList',
  props: {
    items: {
      type: Array,
      default () {
        return []
      }
    }
  },
  data () {
    return {
      druggingStartIndex: null,
      druggingFinishIndex: null
    }
  },
  mounted () {
    /** фиксируем индекс перетаскиваемого элемента */
    document.addEventListener('dragstart', (event) => {
      this.druggingStartIndex = parseInt(event.target.getAttribute('data-index'))
      event.dataTransfer.setData('text/plain', null)
    })

    /** определяем, в какую сторону будет осуществляться вставка (вверх/вниз) */
    document.addEventListener('dragover', (event) => {
      event.preventDefault()
      const bounding = event.target.getBoundingClientRect()
      const offset = bounding.y + (bounding.height / 2)

      /** добавляем соответствующий класс и фиксируем индекс, на которей передвинется элемент */
      if (event.clientY - offset > 0) {
        this.druggingFinishIndex = parseInt(event.target.getAttribute('data-index')) + 1
        event.target.classList.remove('top')
        if (!event.target.classList.contains('bottom')) {
          event.target.classList.add('bottom')
        }
      } else {
        this.druggingFinishIndex = parseInt(event.target.getAttribute('data-index'))
        event.target.classList.remove('bottom')
        if (!event.target.classList.contains('top')) {
          event.target.classList.add('top')
        }
      }
    })

    /** очищаем стили и стейт при выходе */
    document.addEventListener('dragleave', (event) => {
      this.druggingFinishIndex = null
      event.target.classList.remove('top')
      event.target.classList.remove('bottom')
    })

    /** при сбрасывании очищаем стили и изменяем массив */
    document.addEventListener('drop', (event) => {
      event.target.classList.remove('top')
      event.target.classList.remove('bottom')

      if (this.druggingFinishIndex === null) {
        return
      }

      /** менять на прямую пропы нельзя, но в случае с ссылочными типами это допускается */
      const deletedItem = this.items.splice(this.druggingStartIndex, 1)

      if (this.druggingFinishIndex > this.druggingStartIndex) {
        this.druggingFinishIndex--
      }

      console.log(this.druggingStartIndex, this.druggingFinishIndex)
      this.items.splice(this.druggingFinishIndex, 0, ...deletedItem)
    })
  }
}
</script>

<style lang="scss" scoped>
.list-container {
  border: 1px solid #666;
  background-color: white;
}

/* анимация для transition-group */
.list-move {
  transition: transform .3s;
}

.list {
  list-style: none;
  padding: 0;
  margin: 0;
  overflow: hidden;

  &__item {
    padding: 12px 16px;
    cursor: pointer;
    position: relative;

    & + & {
      border-top: 1px solid #ccc;
    }

    &:hover {
      background-color: #eee;
    }

    /* выделение места вставки осуществляется псевдоэлементом */
    &::after {
      content: '';
      z-index: 9999;
      display: none;
      position: absolute;
      left: 0;
      height: 1px; width: 100%;
      background-color: #666666;
      box-shadow: 0 0 3px 0 #666666;
    }

    &.top::after {
      display: block;
      top: -1px;
    }

    &.bottom::after {
      display: block;
      bottom: -1px;
    }
  }
}
</style>
