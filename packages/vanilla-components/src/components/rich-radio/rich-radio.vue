<script setup lang="ts">
import type { PropType } from 'vue'
import { RadioGroup, RadioGroupOption } from '@headlessui/vue'
import RichRadioOption from '../rich-radio-option/rich-radio-option.vue'
import type { InputOptions } from '../../core/types'
import { useConfiguration, useVModel, useVariantProps } from '../../core/use'
import { normalizeOptions } from '../../core/helpers'
import FormErrors from '../forms/form-errors.vue'
import FormFeedback from '../forms/form-feedback.vue'
import { richRadioConfig } from './config'
import type { RichRadioClassesValidKeys, RichRadioProps, RichRadioValue } from './config'

const props = defineProps({
  ...useVariantProps<RichRadioProps, RichRadioClassesValidKeys>(),
  modelValue: {
    type: [String, Number, Boolean, Array, Object, Date, Function, Symbol] as PropType<RichRadioValue>,
    default: undefined,
  },
  disabled: {
    type: Boolean,
    default: false,
  },
  options: {
    type: [Array, Object] as PropType<InputOptions>,
    default: undefined,
  },
  normalizeOptions: {
    type: Boolean,
    default: true,
  },
  valueAttribute: {
    type: String,
    default: 'value',
  },
  textAttribute: {
    type: String,
    default: 'text',
  },
  separated: {
    type: Boolean as PropType<boolean>,
    default: true,
  },
  radio: {
    type: Boolean as PropType<boolean>,
    default: true,
  },
  compact: {
    type: Boolean as PropType<boolean>,
    default: false,
  },
})

const emit = defineEmits(['update:modelValue'])

const localValue = useVModel(props, 'modelValue')
const { configuration, errors, hasErrors, variant } = useConfiguration<RichRadioProps>(richRadioConfig, 'RichRadio', localValue)

const normalizedOptions = normalizeOptions(
  props.options,
  props.textAttribute,
  props.valueAttribute,
)

/**
 * @docs
 * @displayName VanillaRichRadio
 * @description A rich radio component with some fancy design
 **/
</script>

<script lang="ts">
export default {
  name: 'VanillaRichRadio',
  inheritAttrs: false,
}
</script>

<template>
  <div
    class="vanilla-input"
    :class="configuration.classesList?.wrapper"
  >
    <RadioGroup
      v-model="localValue"
      :class="configuration.classesList?.container"
      :disabled="disabled"
    >
      <div
        :class="[
          configuration.classesList?.optionContainer,
          separated ? configuration.classesList?.optionContainerSeparated : configuration.classesList?.optionContainerNonSeparated,
        ]"
      >
        <RadioGroupOption
          v-for="(option, index) in normalizedOptions"
          :key="option.value.toString()"
          v-slot="{ active, checked }"
          as="div"
          :value="option.value"
          :disabled="disabled || option?.disabled || false"
          :class="configuration.classesList?.optionWrapper"
        >
          <slot
            name="option"
            v-bind="{ option, active, checked, index, totalOptions: normalizeOptions.length, separated, radio, variant, disabled: option.disabled || false }"
          >
            <!-- Option -->
            <RichRadioOption
              :option="option"
              :active="active"
              :checked="checked"
              :option-index="index"
              :total-options="normalizedOptions.length"
              :separated="separated"
              :compact="compact"
              :radio="radio"
              :variant="variant"
              :variant-error="variantError"
              v-bind="$attrs"
              :disabled="disabled || option?.disabled || false"
            >
              <template #radioIcon>
                <slot
                  name="radioIcon"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>

              <template #label>
                <slot
                  name="label"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>

              <template #labelText>
                <slot
                  name="labelText"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>

              <template #description>
                <slot
                  name="description"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>

              <template #descriptionText>
                <slot
                  name="descriptionText"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>

              <template #svgIcon>
                <slot
                  name="svgIcon"
                  v-bind="{ option, active, checked, index, compact, radio }"
                />
              </template>
            </RichRadioOption>
          </slot>
        </RadioGroupOption>
      </div>
    </RadioGroup>
    <slot
      name="errors"
      v-bind="{ hasErrors, errors }"
    >
      <FormErrors
        v-if="hasErrors && props.showErrors"
        :errors="errors"
      />
    </slot>
    <slot
      name="feedback"
      v-bind="{ hasErrors, feedback }"
    >
      <FormFeedback
        v-if="!hasErrors && props.feedback && props.showFeedback"
        :text="props.feedback"
      />
    </slot>
  </div>
</template>

