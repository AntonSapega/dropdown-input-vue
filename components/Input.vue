<template>
  <div class="wrapper">
    <form @submit.prevent>
      <label>
        Единица

        <input
          v-model="valueOfInput"
          class="input-valid"
          :class="showError ? 'input-invalid' : 'input-valid'"
          :placeholder="placeholder || defaultPlaceholder"
          @focus="focusHandler"
          @keydown.down="downButton"
          @keydown.up="upButton"
          @keydown.enter="enterAndTabButtons"
          @keydown.tab="enterAndTabButtons"
          @input="inputHandler"
          @blur="blurHandler"
        />
      </label>

      <ul v-if="showList" ref="ulList" class="list">
        <li
          v-for="(unit, index) in modifiedUnits"
          :key="'index' + index"
          :class="isSelected(index) ? 'unitIsActive' : 'unitIsNotActive'"
          @mouseover="hoverImitation(index)"
          @click="clickedUnit"
        >
          {{ unit.name }}
        </li>
      </ul>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Input',
  data() {
    return {
      units: [
        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' },

        { name: 'kg', value: 'kg' },
        { name: 'lit', value: 'lit' },
        { name: 'gr', value: 'gr' },
        { name: 'mm', value: 'mm' },
        { name: 'glob', value: 'glob' },
        { name: 'ml', value: 'ml' },
        { name: 'gramm', value: 'gramm' },
        { name: 'cm', value: 'cm' },
        { name: 'globus', value: 'globus' },
        { name: 'm', value: 'm' }
      ],

      defaultPlaceholder: 'Enter unit',
      placeholder: '',
      valueOfInput: null,
      showList: false,
      activeUnit: null,
      showError: false
    }
  },

  computed: {
    modifiedUnits() {
      if (this.valueOfInput === null) {
        return this.units
      }

      const arrayToShow = []

      this.units.forEach((element) => {
        if (element.value === this.valueOfInput) {
          arrayToShow.unshift(element)
        } else if (element.value.startsWith(this.valueOfInput)) {
          arrayToShow.push(element)
        }
      })

      return arrayToShow
    }
  },

  beforeDestroy() {
    this.setRemoveEventListener()
  },

  methods: {
    focusHandler() {
      this.showList = true
      this.showError = false
    },

    hoverImitation(i) {
      this.activeUnit = i
      this.setPlaceholder()
    },

    isSelected(i) {
      if (i === this.activeUnit) {
        return true
      }
      return false
    },

    inputHandler() {
      this.showError = false

      if (this.valueOfInput === '') {
        this.placeholder = ''
      }

      this.setDefoltState(true)
    },

    upButton() {
      switch (this.activeUnit) {
        case null:
        case 0:
          this.activeUnit = this.modifiedUnits.length - 1
          break
        default:
          this.activeUnit--
      }

      this.setPlaceholder()
      this.scrolling('up')
    },

    downButton() {
      switch (this.activeUnit) {
        case null:
          this.activeUnit = 0
          this.setPlaceholder()
          break

        case this.modifiedUnits.length - 1:
          this.activeUnit = 0
          break
        default:
          this.activeUnit++
      }

      this.setPlaceholder()
      this.scrolling('down')
    },

    enterAndTabButtons() {
      if (this.activeUnit !== null) {
        this.valueOfInput = this.modifiedUnits[this.activeUnit].value
      }

      this.setDefoltState()

      this.showError = !this.valueOfInput // to func
    },

    clickedUnit() {
      if (this.activeUnit !== null) {
        this.valueOfInput = this.modifiedUnits[this.activeUnit].value
      }

      this.setDefoltState()
    },

    blurHandler() {
      document.addEventListener('click', this.clickOutsideComponent)
    },

    clickOutsideComponent(event) {
      if (this.$el && !this.$el.contains(event.target)) {
        if (this.valueOfInput === '') {
          this.showError = true
        }

        this.setDefoltState()
        this.setRemoveEventListener()
      }
    },

    setRemoveEventListener() {
      document.removeEventListener('click', this.clickOutsideComponent)
    },

    setPlaceholder() {
      this.placeholder = this.modifiedUnits[this.activeUnit].value
    },

    setDefoltState(showList = false) {
      this.showList = showList
      this.activeUnit = null
      this.placeholder = ''
    },

    // Динамическое изменение положения скролла в выпадающем списке при использовании стрелок "вверх" или "вниз"
    scrolling(buttonName) {
      const lengthOfScrollBar =
        this.$refs.ulList.scrollHeight - this.$refs.ulList.clientHeight

      switch (buttonName) {
        case 'up':
          if (this.activeUnit === this.modifiedUnits.length - 1) {
            this.$refs.ulList.scrollTo(0, lengthOfScrollBar)
            return
          }

          this.$refs.ulList.scrollBy(
            0,
            -(lengthOfScrollBar / this.modifiedUnits.length)
          )
          break

        case 'down':
          if (this.activeUnit === 0) {
            this.$refs.ulList.scrollTo(0, 0)
            return
          }

          this.$refs.ulList.scrollBy(
            0,
            lengthOfScrollBar / this.modifiedUnits.length + 1
          )
          break
      }
    }
  }
}
</script>

<style scoped>
/* СБРОС СТИЛЕЙ */
*,
*::before,
*::after {
  box-sizing: border-box;
}

/* Убираем внутренние отступы */
ul[class],
ol[class] {
  padding: 0;
}

/* Убираем внешние отступы */
body,
h1,
h2,
h3,
h4,
p,
ul[class],
ol[class],
li,
figure,
figcaption,
blockquote,
dl,
dd {
  margin: 0;
}

/* Выставляем основные настройки по-умолчанию для body */
body {
  min-height: 100vh;
  scroll-behavior: smooth;
  text-rendering: optimizeSpeed;
  line-height: 1.5;
}

/* Удаляем стандартную стилизацию для всех ul и il, у которых есть атрибут class*/
ul[class],
ol[class] {
  list-style: none;
}

/* Элементы a, у которых нет класса, сбрасываем до дефолтных стилей */
a:not([class]) {
  text-decoration-skip-ink: auto;
}

/* Указываем понятную периодичность в потоке данных у article*/
article > * + * {
  margin-top: 1em;
}

/* Наследуем шрифты для инпутов и кнопок */
input,
button,
textarea,
select {
  font: inherit;
}

body {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  letter-spacing: 2px;
  color: rgb(90, 90, 90);
}

.wrapper {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: rgb(240, 238, 238);
}

form {
  position: relative;
}

label {
  margin: 0 0 5px 5px;
  font-size: 18px;
}

.input-valid {
  border: 1px solid rgb(240, 238, 238);
  padding: 0 0 0 10px;
  display: block;
  width: 250px;
  height: 40px;
  font-size: 16px;
  border-width: 1px;
  background-color: white;
  margin-top: 10px;
}

.input-invalid {
  background-color: rgb(252, 168, 153);
}

input:focus,
input:active {
  border-color: #80bdff;
  outline: 0;
  outline-offset: -1px;
}

.list {
  list-style-type: none;
  width: 250px;
  background-color: white;
  margin: 10px 0 0 0;
  padding: 0 0 0 0;
  display: block;
  position: absolute;
  top: 75px;
  overflow: auto;
  max-height: 100px;
}

li {
  padding-left: 10px;
}

.unitIsActive {
  background-color: #80bdff;
}
.unitisNotActive {
  background-color: white;
}

.list li:hover {
  cursor: pointer;
}
</style>
