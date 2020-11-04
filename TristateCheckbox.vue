<script>
export default {
  /*
  Vue.js component implementing a cycling tristate checkbox that supports form submission
  Copyright (C) 2020 Sebastian Pipping <sebastian@pipping.org>
  Licensed under the MIT license
  5ed566ba1746f5a4d47330308b435ce006e21782ef58d7c741edf25b59b21ab7
*/
  props: {
    binary: {
      // Whether to revert back to a binary checkbox, wins over indeterminate
      type: Boolean,
      default: false
    },
    checked: {
      // Whether to start out checked
      type: Boolean,
      default: false
    },
    disabled: {
      // Whether to start out disabled
      type: Boolean,
      default: false
    },
    // eslint-disable-next-line vue/require-default-prop
    id: {
      // HTML ID to use for the checkbox node of the component
      type: String
    },
    indeterminate: {
      // Whether to start out indeterminate, wins over checked
      type: Boolean,
      default: false
    },
    name: {
      // Name to use during form submission; set to enable form submission
      type: String,
      default: ''
    },

    trueValue: {
      // Model value to use for checked state
      type: Boolean,
      default: true
    },
    falseValue: {
      // Model value to use for unchecked state
      type: Boolean,
      default: false
    },
    // eslint-disable-next-line vue/require-prop-types
    nullValue: {
      // Model value to use for indeterminate state
      default: null
    },

    value: {
      // Form submission value to use for checked state
      type: String,
      default: 'on'
    },
    valueIndeterminate: {
      // Form submission value to use for indeterminate state
      type: String,
      default: 'indeterminate'
    },
    // eslint-disable-next-line vue/prop-name-casing,vue/require-default-prop,vue/require-prop-types
    _external_state: {
      // Target for v-model when given (think this.$vnode.data.model.value)
    }
  },
  data () {
    return {
      internal_state: this.indeterminate ? this.nullValue : (this.checked ? this.trueValue : this.falseValue)
    }
  },
  computed: {
    state: {
      get: function () {
        if (this.$vnode.data.model) {
          return this._external_state
        } else {
          return this.internal_state
        }
      },
      set: function (newState) {
        this.internal_state = newState
        this.apply_to_checkbox(newState)
      }
    },

    // Value used for hidden field during form sumbission
    submission_value: function () {
      switch (this.state) {
        case this.trueValue:
          return this.value
        case this.nullValue:
          return this.valueIndeterminate
        case this.falseValue:
        default:
          return false // i.e. no submission as a plain checkbox would do
      }
    }
  },
  created () {
    if (this.icon !== '') {
      this.extraClasses += 'fullSize iconized'
    }
  },
  mounted: function () {
    this.apply_to_checkbox(this.state)
  },
  methods: {
    apply_to_checkbox: function (state) {
      const checkbox = this.$el.firstElementChild
      checkbox.checked = (state === this.trueValue)
      checkbox.indeterminate = (state === this.nullValue)
    },
    toggle: function () {
      switch (this.state) {
        case this.falseValue:
          this.state = this.nullValue
          break
        case this.trueValue:
          this.state = this.falseValue
          break
        case this.nullValue:
          this.state = this.trueValue
          break
      }
    }
  },
  // eslint-disable-next-line no-multi-str
  template: '\
    <div style="display: inline-block" ref="container"> \
      <input type="checkbox" :id="id || false" :disabled="disabled" \
        @click.stop="toggle" \
        @keyup.space.prevent="toggle" \
        ref="checkbox" \
        > \
      <input v-if="name && submission_value" \
        type="hidden" :name="name" \
        :value="submission_value" \
        ref="hidden-input" \
      > \
    </div> \
    '
}
</script>
