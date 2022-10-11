<template>
  <b-container>
    <b-row class="justify-content-center">
      <b-col sm="10">
        <h1>{{ msg }}</h1>
      </b-col>
    </b-row>
    <b-row class="mt-5 justify-content-center">
      <b-col sm="10">
        <div>
          <header class="header-textarea d-flex justify-content-between mb-2">
            <div class="text-left">Condition Expresion <b-icon class="header-icon" icon="question-circle-fill" aria-hidden="true"></b-icon></div>
            <div class="d-flex">
              <span>Raw Mode:</span><b-form-checkbox v-model="checkedRaw" name="check-button" switch>Disabled</b-form-checkbox>
            </div>
          </header>
          <b-form-textarea
            id="textarea"
            v-model.trim="textExpression"
            placeholder="Enter something..."
            rows="8"
            @click="isActive = !isActive"
          >

        </b-form-textarea>
        </div>
      </b-col>
    </b-row>
    <b-row class="justify-content-center" v-if="!checkedRaw">
      <b-col sm="10">
        <p class="mt-2 description-textarea">Click to the textarea above for editing expresion</p>
      </b-col>
    </b-row>
    <ConstructorPopup
      v-if="isActive && !checkedRaw"
      @expression='createExpression'
      :textExpression="textExpression"
    />
  </b-container>
</template>

<script>
import ConstructorPopup from './popup/ConstructorPopup.vue'

export default {
  name: 'ConstructorView',
  components: {
    ConstructorPopup
  },
  props: {
    msg: String,
  },
  data() {
    return {
      textExpression: '',
      checkedRaw: false,
      isActive: false,
      expressions: [],
      line: []
    }
  },
  methods: {
    // Тут я обрабатываю массив с регулярными выражениями, который получаю через props
    createExpression(data) {
      this.expressions.push(data);
      this.renderItem(this.expressions);
      this.line.forEach(this.renderExpressionItem);

      this.expressions.length = 0;
      this.line.length = 0;

    },
    // Это рекукрсивная функции для получения значений из массивва с объектами
    renderItem(obj) {
      for (var item of Object.values(obj))
        if (typeof item === 'object') {
          this.renderItem(item)
        } else {
            this.line.push(item);
        }
    },
    // Тут я передаю строковые данные в v-model textarea
    renderExpressionItem(item) {
      item !== '' ? this.textExpression += item + ' ' : false;
    }
  }
}
</script>

<style lang="scss">
.header-textarea {

  .header-icon {
    cursor: pointer;
  }
}

.description-textarea {
  text-align: left;
  font-size: 12px;
}

.custom-control-input {
  cursor: pointer;
  margin: 0 5px;
}
</style>
