<template>
  <div class="field-wrapper">
    <div class="field-wrapper__form-element"><slot></slot></div>
    <div
      class="field-wrapper__error text-sm"
      :class="{ 'field-wrapper__error--is-visible': errorText }"
    >
      {{ errorText }}
    </div>
  </div>
</template>

<script lang="ts">
import { Vue, Component, Prop } from "nuxt-property-decorator";
@Component({ components: {} })
export default class FieldWrapper extends Vue {
  @Prop() errorText: string;
}
</script>

<style lang="less">
.field-wrapper {
  // .field-wrapper__error
  &__error {
    transition: @transition-mod;
    color: @color-accent;
    max-height: 0;
    opacity: 0;
    pointer-events: none;

    // .field-wrapper__error--is-visible
    &--is-visible {
      max-height: 50px;
      opacity: 1;
      pointer-events: unset;
      margin-top: 3px;

      @media (max-width: @breakpoint-tablet) {
        margin-top: 4px;
      }
      @media (max-width: @breakpoint-tablet-sm) {
        margin-top: 5px;
      }
    }
  }
}
</style>
