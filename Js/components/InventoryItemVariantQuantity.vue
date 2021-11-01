<template>
  <b-row class="item_variant pr-5">
    <b-button
      class="close"
      size="sm"
      variant="danger"
      v-b-tooltip.hover
      :title="$t('buttons.delete')"
      @click="dropItem()"
      v-if="!disabled && !deletable"
    >
      <i class="fe fe-x"></i>
    </b-button>

    <b-col>
      <b-row>
        <b-form-group
          class="col-sm-12 col-md-6 col-xl-3"
          :label="v.name"
          :label-for="v.name"
          :label-cols="4"
          label-class="font-weight-bold"
          v-for="(v, i) in value.variations"
          :key="i"
          :invalid-feedback="
            feedback('variations.' + index + '.variations.' + i + '.value')
          "
          :state="state('variations.' + index + '.variations.' + i + '.value')"
        >
          <multiselect
            :id="v.name"
            :name="v.name"
            :searchable="true"
            v-model="v.value"
            :options="v.options"
            :close-on-select="true"
            :show-labels="false"
            :placeholder="'-- ' + v.name + ' --'"
            @input="variantSelected"
          ></multiselect>
        </b-form-group>

        <b-form-group
          class="col-sm-12 col-md-6 col-xl-3"
          :label="$t('validation.attributes.quantity')"
          label-for="quantity"
          :label-cols="4"
          label-class="font-weight-bold"
          :invalid-feedback="feedback(quantity_field)"
          :state="state(quantity_field)"
        >
          <b-input-group>
            <b-form-input
              id="quantity"
              name="quantity"
              :placeholder="$t('validation.attributes.quantity')"
              v-model="value.quantity"
              :state="state(quantity_field)"
            ></b-form-input>
            <b-input-group-text
              slot="prepend"
              v-if="value.available_quantity !== null"
              ><strong
                :class="{
                  'text-danger': value.available_quantity === 0,
                  'text-success': value.available_quantity > 0
                }"
                >{{ value.available_quantity }}</strong
              ></b-input-group-text
            >
            <b-input-group-text slot="append" v-if="unit"
              ><strong class="text-success">{{
                unit
              }}</strong></b-input-group-text
            >
          </b-input-group>
        </b-form-group>
      </b-row>
    </b-col>
  </b-row>
</template>
<script>
import TagsInput from '@voerro/vue-tagsinput'
import '@voerro/vue-tagsinput/dist/style.css'
import Multiselect from 'vue-multiselect'
import EventBus from '../../lib/event-bus'

export default {
  name: 'InventoryItemVariantQuantity',
  components: { Multiselect },
  props: {
    value: {
      default: () => ({
        id: null,
        available_quantity: null,
        quantity: null,
        variations: {
          name: null,
          options: {},
          value: null
        }
      }),
      required: true,
      type: [Object, Array]
    },
    index: {
      default: 0,
      type: Number
    },
    unit: {
      default: null,
      type: String
    },
    disabled: {
      type: Boolean,
      default: false,
      required: false
    },
    deletable: {
      type: Boolean,
      default: false,
      required: false
    },
    state: {
      type: Function,
      required: true
    },
    feedback: {
      type: Function,
      required: true
    }
  },
  data() {
    return {}
  },
  computed: {
    name_field: function() {
      return 'variations.' + this.index + '.name'
    },
    options_field: function() {
      return 'variations.' + this.index + '.options'
    },
    quantity_field: function() {
      return 'variations.' + this.index + '.quantity'
    }
  },
  methods: {
    // updateInput: function(i) {
    //   console.log(this.value.variations[i].value)
    //   var value = this.value.variations[this.index].value
    //   this.$emit('input', {
    //     variations: {
    //       value: value
    //     },
    //     quantity: this.value.quantity
    //   })
    // },
    // feedback(name) {
    //   if (this.state(name)) {
    //     return this.$parent.$parent.validation.errors[name][0]
    //   }
    // },
    // state(name) {
    //   return this.$parent.$parent.validation.errors !== undefined &&
    //     this.$parent.$parent.validation.errors.hasOwnProperty(name)
    //     ? 'invalid'
    //     : null
    // },
    dropItem() {
      this.$emit('deleted', { index: this.index })
    },
    variantSelected() {
      EventBus.$emit('VARIANT_CHANGED', this.value.variations, this.index)
    }
  }
}
</script>
<style scoped>
.item_variant {
  background-color: #f0fffd;
  padding-top: 8px;
  margin-top: 5px;
  margin-bottom: 5px;
  border-radius: 3px;
  border: 1px solid #e9ecef;
  position: relative;
}
.item_variant:hover {
  background-color: #e4fffd;
  box-shadow: 0 0 8px rgba(0, 0, 0, 0.6);
}
.close {
  position: absolute;
  right: 0px;
  top: 0px;
}
</style>
