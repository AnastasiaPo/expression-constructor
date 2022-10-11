<template>
  <b-row class="d-flex align-items-center">
    <b-col sm="3">
      <b-form-select v-model="selectedFields">
        <option v-for="(val, key) in fields" :key="key">{{ val }}</option>
      </b-form-select>
    </b-col>
    <b-col sm="3">
      <b-form-select v-model="selectedOps">
        <option v-for="(val, key) in renderOps(selectedFields)" :key="key">{{ val }}</option>
      </b-form-select>
    </b-col>
    <b-col sm="5" v-if="selectedFields === 'country'">
      <b-form-select
        v-model="selectedCountry"
        @change="createContent"
        :disabled="isEmpty()"
      >
        <option v-for="(val, key) in country" :key="key">{{ val }}</option>
      </b-form-select>
    </b-col>
    <b-col sm="5" v-if="selectedFields === 'name'">
      <b-form-input
        v-model.trim="selectedName"
        @change="createContent"
        :disabled="isEmpty()"
        placeholder="Enter name"
        size="sm"
      />
    </b-col>
    <b-col sm="5" v-if="selectedFields === 'id'">
      <b-form-input
        v-model.number="selectedId"
        @change="createContent"
        :disabled="isEmpty()"
        placeholder="Enter id"
        type="number"
        size="sm"
      />
    </b-col>
    <b-col sm="1" class="text-right">
      <b-button
        class="rule-btn"
        size="sm"
        variant="outline-secondary"
        @click="removeItem"
      >
        <b-icon class="header-icon" icon="trash-fill" aria-hidden="true" />
      </b-button>
    </b-col>
  </b-row>
</template>

<script>

  export default {
    name: 'ConstructorItemRow',
    props: {
      variables: Object,
      onSelected: Function,
    },
    data() {
      return {
        selectedFields: 'country',
        selectedOps: {},
        selectedCountry: {},
        selectedId: '',
        selectedName: '',
      }
    },
    computed: {
      fields() {
       return Object.keys(this.variables.fields);
      },
      country() {
       return Object.values(this.variables.fields.country.choices);
      },
    },
    methods: {
      removeItem() {
        this.$emit('remove');
      },
      // Тут рендерятся поля с ops.
      // Вообще мне не нравится эта функция. Ее (и условия с отображением данных в template) можно круче сделать
      renderOps(key) {
        if(key === 'country') {
          return Object.values(this.variables.fields.country.ops);
        } else if(key === 'name') {
          return Object.values(this.variables.fields.name.ops);
        } else if (key === 'id') {
          return Object.values(this.variables.fields.id.ops);
        }
      },
      // Тут я отслеживаю изменения в инпутах и отправляю введенные данные в v-model
      createContent() {
        this.onSelected({
          selectedFields: this.selectedFields,
          selectedOps: this.selectedOps,
          selectedCountry: this.selectedCountry,
          selectedId: this.selectedId,
          selectedName: this.selectedName,
        })
      },
      // Проверяю что все поля заполнены и убираю disabled с инпута
      isEmpty() {
        return Object.keys(this.selectedOps).length === 0;
      }
    }
  }
</script>

<style lang="scss">
.text-right {
  text-align: right;
}

.custom-select {
  width: 100%;
  padding: 0.25rem;
}
</style>
