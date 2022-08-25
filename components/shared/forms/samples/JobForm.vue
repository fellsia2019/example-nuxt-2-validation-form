<template>
  <!-- обвёртка для форм -->
  <FormWrapper>
    <!-- обвёртка для полей формы -->
    <FieldWrapper :errorText="currentFormErrors.name">
      <CustomInput
        v-model="form.name"
        placeholder="Иванов Иван"
        :error="!!currentFormErrors.name"
      >
        Ваше имя
      </CustomInput>
    </FieldWrapper>

    <FieldWrapper :errorText="currentFormErrors.tel">
      <CustomInput
        v-model="form.tel"
        type="tel"
        :error="!!currentFormErrors.tel"
        >Телефон*
      </CustomInput>
    </FieldWrapper>

    <FieldWrapper :errorText="currentFormErrors.email">
      <CustomInput
        v-model="form.email"
        placeholder="ivanovinan@gmail.com"
        :error="!!currentFormErrors.email"
        >E-mail*
      </CustomInput>
    </FieldWrapper>

    <FieldWrapper
      :errorText="currentFormErrors.file"
      class="self-bottom-xl end-xl start-md"
    >
      <CustomInputFile
        v-model="form.file"
        placeholder="placeholder"
        :maxLengthFileName="10"
        >Прикрепить резюме</CustomInputFile
      >
    </FieldWrapper>

    <FieldWrapper :errorText="currentFormErrors.checkboxValue">
      <CustomCheckbox
        @change="(v) => (form.checkboxValue = v)"
        :checked="form.checkboxValue"
        :error="!!currentFormErrors.checkboxValue"
        href="/agreement/"
      >
        <template v-slot:checkbox-text>Принимаю </template>
        <template v-slot:checkbox-link>политику конфиденциальности</template>
      </CustomCheckbox>
    </FieldWrapper>

    <CustomButton
      :theme="ButtonTheme.ACCENT"
      :size="ButtonSize.MD"
      :isLoading="isLoading"
      @click="handleSubmit()"
    >
      {{ buttonText }}
    </CustomButton>
  </FormWrapper>
</template>

<script lang="ts">
import CustomButton from "@/components/common/controls/CustomButton.vue";
import CustomCheckbox from "@/components/common/inputs/CustomCheckbox.vue";
import CustomInput from "@/components/common/inputs/CustomInput.vue";
import CustomSelect from "@/components/common/inputs/CustomSelect.vue";
import CustomInputFile from "@/components/common/inputs/CustomInputFile.vue";
import FormWrapper from "@/components/shared/forms/wrappers/FormWrapper.vue";
import { Vue, Component, Prop } from "nuxt-property-decorator";
import { ButtonSize, ButtonTheme } from "~/types/common";
import FieldWrapper from "@/components/shared/forms/wrappers/FieldWrapper.vue";
import {
  IFormState,
  IPartialOfDeaultValidation,
  validateForm,
} from "~/helpers/validationHelpers";
import { captcha } from "~/helpers/captchaHelper";

@Component({
  components: {
    CustomButton,
    CustomCheckbox,
    CustomInput,
    CustomSelect,
    CustomInputFile,
    FormWrapper,
    FieldWrapper,
  },
})
export default class JobForm extends Vue {
  @Prop() buttonText: string;
  @Prop() isLoading: boolean;

  ButtonTheme: typeof ButtonTheme = ButtonTheme;
  ButtonSize: typeof ButtonSize = ButtonSize;

  // дата формы
  form = {
    name: "",
    tel: "",
    email: "",
    file: "",
    checkboxValue: false,
  };

  // объект с настройками валидации по имени поля из form
  rules: IFormState = {
    name: {
      state: {
        // объект для текстов ошибок
        errorsObject: {},
      },
      rules: {
        // объект объявление правил валидации
        maxLen: 50,
        nameSymbol: true,
      },
    },
    tel: {
      state: {
        errorsObject: {},
      },
      rules: {
        required: true,
        phone: true,
      },
    },
    email: {
      state: {
        errorsObject: {},
      },
      rules: {
        required: true,
        email: true,
        maxLen: 50,
      },
    },
    file: {
      state: {
        errorsObject: {},
      },
      rules: {
        required: true,
        fileSize: true,
        fileName: true,
        fileExtension: true,
      },
    },
    checkboxValue: {
      state: {
        errorsObject: {},
      },
      rules: {
        required: true,
      },
    },
  };

  currentFormErrors = {
    name: "",
    tel: "",
    email: "",
    file: "",
    checkboxValue: "",
  };

  calculateErrorText(errorsList: IPartialOfDeaultValidation) {
    let resultError;
    if (errorsList.required) resultError = errorsList.required;
    else {
      for (let errorText in errorsList) {
        if (errorsList[errorText]) resultError = errorsList[errorText];
      }
    }

    return resultError;
  }

  setErrors() {
    for (let rule in this.rules) {
      // @ts-ignore
      this.currentFormErrors[rule] = this.calculateErrorText(
        this.rules[rule].state.errorsObject
      );
    }
  }

  handleSubmit() {
    let isValid = !validateForm({ form: this.form, rules: this.rules });
    this.setErrors();

    if (isValid) return;

    this.onSubmit();
  }
  async onSubmit() {
    this.$emit("submit", {
      name: this.form.name.trim(),
      phone: `7${this.form.tel}`,
      email: this.form.email.trim(),
      file: this.form.file,
      captchaToken: await captcha(this.$recaptcha),
    });
    this.form = {
      name: "",
      tel: "",
      email: "",
      file: "",
      checkboxValue: false,
    };
  }
}
</script>

<style lang="less"></style>
