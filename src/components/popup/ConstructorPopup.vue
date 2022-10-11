<template>
  <b-modal
    v-model="modalShow"
    id="modal-prevent-closing"
    ref="modal"
    title="Condition Expresions"
    hide-footer
    size="lg"
  >
    <form ref="form" @submit.stop.prevent="handleSubmit">
      <div class="d-flex">
        <b-button-group size="sm">
          <b-button variant="outline-secondary" @click="getCondition('AND')" :disabled="disabledExpression">AND</b-button>
          <b-button variant="outline-secondary" @click="getCondition('OR')" :disabled="disabledExpression">OR</b-button>
        </b-button-group>
        <b-button
          class="rule-btn"
          size="sm"
          variant="outline-secondary"
          :disabled="disabledRule"
          @click="addRule"
        >
          <b-icon
            class="header-icon"
            icon="plus"
            aria-hidden="true"
          >
          </b-icon>
            Rule
        </b-button>
      </div>
      <div
        class="mt-3"
        v-for="(item, index) in rules"
        :key="item"
        :id="`rule-${index}`"
      >
        <ConstructorItemRow
          :variables="variables"
          @remove="removeRule(index)"
          :onSelected="onSelected"
        />
      </div>
    </form>
    <b-row class="mt-4">
      <b-col sm="6">
        <b-button size="sm" @click="hideModal">Close</b-button>
      </b-col>
      <b-col sm="6" class="text-right">
        <b-button
          size="sm"
          variant="success"
          @click="onSubmit"
          :disabled="expression.length === 0"
        >
        Apply changes
      </b-button>
      </b-col>
    </b-row>
  </b-modal>
</template>

<script>
import variablesData from "../../variables.json";
import ConstructorItemRow from "./ConstructorItemRow.vue";

export default {
  name: 'ConstructorPopup',
  components: {
    ConstructorItemRow
  },
  props: {
    textExpression: String,
  },
  data() {
    return {
      modalShow: true,
      variables: variablesData,
      rules: [],
      expression: [],
      disabledExpression: true,
      disabledRule: false,
    }
  },
  // Проверяю в хуке создания компоненты с чего надо начинать ввод в попапе - с условия AND/OR или с нового rule
  created() {
    this.checkLastWord();
  },
  methods: {
    hideModal() {
      this.$refs['modal'].hide();

      this.resetSelected();
    },
    // Эта функция добавляет новувю строку с логическким выражением, а так же регулирует disabled у кнопок, чтобы избежать ошибок
    addRule() {
      this.rules.push(this.rules.length + 1);
      this.disabledRule = true;
      this.disabledExpression = false;
    },
    // Эта функция про удаления логического выражения (по нажатию на кнопку с корзиной) из интерфейса и из модели данных,
    // еще тут проверка disabled кнопок для случая если добавили новое правило, а потом сразу удалили его
    removeRule(index) {
      this.rules.splice(index, 1);
      this.expression.splice(index, 2);

      if(this.rules.length === 0) {
        this.disabledRule = false;
        this.disabledExpression = true;
      }

      this.resetSelected();
    },
    // Эта функция добавляет в массив с данными значение AND/OR
    getCondition(val) {
      this.expression.push(val);
      this.disabledRule = false;
      this.disabledExpression = true;
    },
    // Тут я формирую массив с данными из компоненты, в которой формируются item
    onSelected(data) {
      this.expression.push(data);
    },
    // Эта функция удаляет все данные из массива с логическими выражениями, если строки удалены в интрефейсе.
    // Просто проверка, чтобы ничего лишнего не записалось =)
    resetSelected() {
      if(this.rules.length === 0) {
        this.expression.length = 0;
      }
    },
    onSubmit() {
      this.$emit('expression', this.expression);
      this.hideModal();
    },
    // Тут я проверяю последнее слово, которое попало в textarea
    // и (через disabled кнопок) показываю с чего надо начинать ввод в попапе - с условия AND/OR или с нового rule.
    // Вызываю ф-ию в хуке created
    checkLastWord() {
      let str = this.textExpression.trim();
      let lastWord = str.slice(str.lastIndexOf(' ')).trim();

      if(str !== '') {
        if(lastWord === 'AND' || lastWord === 'OR') {
          this.disabledRule = false;
          this.disabledExpression = true;
        } else {
          this.disabledRule = true;
          this.disabledExpression = false;
        }
      }
    }
  },
}
</script>

<style lang="scss">
.rule-btn {
  margin-left: auto;
}
.close {
  background: transparent;
  border: none;
  font-size: 25px;
  position: absolute;
  right: 16px;
  top: 7px;
  padding: 0;

  &:hover {
    opacity: 0.7;
  }
}
</style>
