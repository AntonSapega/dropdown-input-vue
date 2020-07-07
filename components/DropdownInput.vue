<template>
  <form ref="form" class="form-class" @submit.prevent>
    <label ref="label" class="label-class">
      {{ label }}
    </label>

    <input
      v-model="valueOfInput"
      class="input-class"
      :class="[
        showError ? 'input-invalid' : '',
        showList ? 'list-is-shown' : 'list-no-shown'
      ]"
      :placeholder="placeholder || defaultPlaceholder"
      @focus="focusHandler"
      @keydown.down="downButton"
      @keydown.up="upButton"
      @keydown.enter="enterAndTabButtons"
      @keydown.tab="enterAndTabButtons"
      @input="inputHandler"
    />

    <ul v-if="showList" ref="ulList" class="list">
      <li
        v-for="(unit, index) in modifiedUnits"
        ref="liElement"
        :key="'index' + index"
        class="liElement"
        :class="isSelected(index) ? 'unitIsActive' : 'unitIsNotActive'"
        @mouseover="!preventMouse ? hoverImitation(index) : ''"
        @click="clickedUnit"
      >
        <span> {{ unit.name }} </span>
      </li>
    </ul>
    <div class="arrow-down-img" @click="clickedArrowImage"></div>
  </form>
</template>

<script>
export default {
  name: 'DropdownInput',

  props: {
    label: {
      type: String,
      required: true
    },

    defaultPlaceholder: {
      type: String,
      default: 'Enter unit'
    },

    units: {
      type: Array,
      required: true
    }
  },

  data() {
    return {
      placeholder: '',
      valueOfInput: null,
      showList: false,
      activeUnit: null,
      showError: false,
      clickListener: false,
      preventMouse: false
    }
  },

  computed: {
    modifiedUnits() {
      if (this.valueOfInput === null) {
        return this.units
      }

      const arrayToShow = []

      this.units.forEach((element) => {
        if (element.value.toLowerCase() === this.valueOfInput.toLowerCase()) {
          arrayToShow.unshift(element)
        } else if (
          element.value
            .toLowerCase()
            .startsWith(this.valueOfInput.toLowerCase())
        ) {
          arrayToShow.push(element)
        }
      })

      return arrayToShow
    }
  },

  watch: {
    showList(value) {
      if (value && !this.clickListener) {
        document.addEventListener('click', this.clickOutsideComponent)
      }
    }
  },

  beforeDestroy() {
    this.callRemoveEventListener()
  },

  methods: {
    focusHandler() {
      this.showList = true
      this.showError = false
      // this.placeholder = ''
    },

    isSelected(i) {
      if (i === this.activeUnit) {
        return true
      }

      return false
    },

    inputHandler() {
      if (this.valueOfInput === '') {
        this.placeholder = ''
      }

      // this.emptyInputValidate()
      this.setDefoltState(true)
      this.searchedElement()
    },

    hoverImitation(i) {
      this.activeUnit = i
    },

    searchedElement() {
      let haveCoincidence = false
      this.modifiedUnits.forEach((elem, i) => {
        if (elem.value.toLowerCase() === this.valueOfInput.toLowerCase()) {
          if (haveCoincidence === false) {
            this.activeUnit = i
            haveCoincidence = true

            // Скроллим вверх, чтобы отобразить найденный элемент
            const list = this.$refs.ulList
            list.scrollTop = 0
          }
        }
      })

      // why it is do not work?
      // for (const i in this.modifiedUnits) {
      //   console.log(this.modifiedUnits[i].value)
      //   if (this.modifiedUnits[i].value === this.valueOfInput) {
      //     this.activeUnit = i
      //   }
      //   break
      // }
      // console.log(this.activeUnit)

      // why it is do not work?
      // this.modifiedUnits.some((item, index) => {
      //   console.log(index)
      //   console.log(this.valueOfInput === item.value)
      //   return (this.activeUnit =
      //     this.valueOfInput.toLowerCase() === item.value.toLowerCase()
      //       ? index
      //       : null)
      // })
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

      this.scrolling()
    },

    downButton() {
      switch (this.activeUnit) {
        case null:
          this.activeUnit = 0
          break
        case this.modifiedUnits.length - 1:
          this.activeUnit = 0
          break
        default:
          this.activeUnit++
      }

      this.scrolling()
    },

    enterAndTabButtons() {
      if (this.activeUnit !== null) {
        this.valueOfInput = this.modifiedUnits[this.activeUnit].value
      }

      this.setDefoltState()
      this.emptyInputValidate()
    },

    emptyInputValidate() {
      this.showError = !this.valueOfInput
    },

    // clickedUnit(item, i) {
    //   this.valueOfInput = item.value
    //   this.setDefoltState()
    // },

    clickedUnit() {
      if (this.activeUnit !== null) {
        this.valueOfInput = this.modifiedUnits[this.activeUnit].value
      }

      this.setDefoltState()
    },

    clickedArrowImage() {
      this.showList = !this.showList
    },

    clickOutsideComponent(event) {
      if (
        (this.$el && !this.$el.contains(event.target)) || // клик вне компонента
        (this.$el && this.$refs.label === event.target) || // клик по label
        (this.$el && this.$refs.form === event.target) // клик по самой форме
      ) {
        this.clickListener = true
        this.emptyInputValidate()
        this.setDefoltState()
        this.callRemoveEventListener()
      }
    },

    callRemoveEventListener() {
      document.removeEventListener('click', this.clickOutsideComponent)
      this.clickListener = false
    },

    setDefoltState(showList = false) {
      this.showList = showList
      this.activeUnit = null
      // this.placeholder = ''  ВОЗМОЖНО НУЖНО УДАЛИТЬ
    },

    // Динамическое изменение положения скролла в выпадающем списке при использовании стрелок "вверх" или "вниз"
    scrolling() {
      const list = this.$refs.ulList
      const element = this.$refs.liElement[this.activeUnit]

      const visMin = list.scrollTop
      const visMax = list.scrollTop + list.clientHeight - element.clientHeight

      if (element.offsetTop < visMin) {
        list.scrollTop = element.offsetTop
        this.preventMouseHandler()
      } else if (element.offsetTop >= visMax) {
        list.scrollTop =
          element.offsetTop - list.clientHeight + element.clientHeight
        this.preventMouseHandler()
      }
    },

    // Отключение hover-иммитации для мыши (для корректного поведения скроллинга при использоаванни стрелок "вверх" и "вниз")
    preventMouseHandler() {
      this.preventMouse = true
      this.$el.addEventListener('mousemove', this.onHoverImitation)
    },

    onHoverImitation() {
      this.preventMouse = false
      this.removeEventOfMove()
    },

    removeEventOfMove() {
      this.$el.removeEventListener('mousemove', this.onHoverImitation)
    }
  }
}
</script>

<style scoped>
@import './resetStyles.scss';

/* body {
  font-family: Verdana, Geneva, Tahoma, sans-serif;
  letter-spacing: 2px;
  color: rgb(90, 90, 90);
} */

/* .wrapper { */
/* display: flex; */
/* flex-direction: column; */
/* justify-content: center; */
/* align-items: center; */
/* height: 100vh; */
/* width: 185px;
  background-color: rgb(240, 238, 238);
} */

.form-class {
  position: relative;
  width: 185px;
}

.label-class {
  /* margin: 0 0 5px 5px;
  font-size: 18px; */

  position: absolute;
  width: 250px;
  height: 20px;
  left: 0px;
  top: 0px;

  font-family: Lato;
  font-style: normal;
  font-weight: 600;
  font-size: 14px;
  line-height: 20px;
  color: #262d33;
}

.input-class {
  /* border: 1px solid rgb(240, 238, 238);
  padding: 0 0 0 10px;
  display: block;
  width: 185px;
  height: 40px;
  font-size: 16px;
  border-width: 1px;
  background-color: white;
  margin-top: 10px; */

  position: absolute;
  height: 40px;
  width: 100%;
  top: 25px;
  padding: 10px 0 10px 12px;

  background: #f7f8f9;
  border-radius: 5px;
  border: 1px solid #f7f8f9;
  font-size: 14px;
  box-sizing: border-box;
}

.input-invalid {
  background-color: rgb(252, 168, 153);
}

/* input:focus,
input:active {
  border-color: #80bdff;
  outline: 0;
  outline-offset: -1px;
  border-radius: 5px 5px 0 0;
  border-bottom: 1px solid #d9dadb;
} */

.list-no-shown {
  outline: 0;
  outline-offset: -1px;
}

.list-is-shown {
  border-color: #80bdff;
  outline: 0;
  outline-offset: -1px;
  border-radius: 5px 5px 0 0;
  border-bottom: 1px solid #d9dadb;
}

.list {
  list-style-type: none;
  width: 185px;
  background-color: white;
  border: 1px solid #80bdff;
  border-top: 1px solid #ffffff;
  border-radius: 0 0 5px 5px;
  margin: 10px 0 0 0;
  padding: 0 0 0 0;
  display: block;
  position: absolute;
  top: 65px;
  overflow: auto;
  max-height: 200px;
  scrollbar-color: #4b5157 white; /* thumb and track color (style for Firefox)*/
  scrollbar-width: thin; /*style for Firefox*/
}

.liElement {
  padding-top: 10px;
  padding-left: 15px;
  height: 40px;
  border-bottom: 1px solid #d9dadb;
}

/* .liElement:hover {
  background-color: #e7edf5;
  cursor: pointer;
} */

.unitIsActive {
  background-color: #80bdff;
}
.unitisNotActive {
  background-color: white;
}

.arrow-down-img {
  position: absolute;
  right: 10px;
  top: 40px;

  width: 8px;
  height: 8px;
  border: solid #adb3ba;
  border-width: 0 1px 1px 0;
  display: inline-block;
  transform: rotate(45deg);
  -webkit-transform: rotate(45deg);
}

.arrow-down-img:hover {
  border: solid #71c28c;
  border-width: 0 2px 2px 0;
  cursor: pointer;
}

::-webkit-scrollbar {
  width: 9px;
}

::-webkit-scrollbar-track {
  z-index: -1;
}

::-webkit-scrollbar-thumb {
  background: #4b5157;
  border-radius: 8px;
  border: 2px solid #ffffff;
}
</style>
