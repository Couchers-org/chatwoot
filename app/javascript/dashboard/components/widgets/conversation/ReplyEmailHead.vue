<script>
import { validEmailsByComma } from './helpers/emailHeadHelper';
import { useVuelidate } from '@vuelidate/core';
import ButtonV4 from 'dashboard/components-next/button/Button.vue';

export default {
  components: {
    ButtonV4,
  },
  props: {
    ccEmails: {
      type: String,
      default: '',
    },
    bccEmails: {
      type: String,
      default: '',
    },
    toEmails: {
      type: String,
      default: '',
    },
    emailSubject: {
      type: String,
      default: '',
    },
  },
  emits: [
    'update:bccEmails',
    'update:ccEmails',
    'update:toEmails',
    'update:emailSubject',
  ],
  setup() {
    return { v$: useVuelidate() };
  },
  data() {
    return {
      showSubject: false,
      showCc: false,
      showBcc: false,
      ccEmailsVal: '',
      bccEmailsVal: '',
      toEmailsVal: '',
      emailSubjectVal: '',
    };
  },
  computed: {
    hasHiddenFields() {
      return !this.showSubject || !this.showCc || !this.showBcc;
    },
  },
  watch: {
    bccEmails(newVal) {
      if (newVal !== this.bccEmailsVal) {
        this.bccEmailsVal = newVal;
      }
      if (newVal) this.showBcc = true;
    },
    ccEmails(newVal) {
      if (newVal !== this.ccEmailsVal) {
        this.ccEmailsVal = newVal;
      }
      if (newVal) this.showCc = true;
    },
    toEmails(newVal) {
      if (newVal !== this.toEmailsVal) {
        this.toEmailsVal = newVal;
      }
    },
    emailSubject(newVal) {
      if (newVal !== this.emailSubjectVal) {
        this.emailSubjectVal = newVal;
      }
    },
  },
  mounted() {
    this.ccEmailsVal = this.ccEmails;
    this.bccEmailsVal = this.bccEmails;
    this.toEmailsVal = this.toEmails;
    this.emailSubjectVal = this.emailSubject;
    this.showCc = !!this.ccEmails;
    this.showBcc = !!this.bccEmails;
  },
  validations: {
    ccEmailsVal: {
      hasValidEmails(value) {
        return validEmailsByComma(value);
      },
    },
    bccEmailsVal: {
      hasValidEmails(value) {
        return validEmailsByComma(value);
      },
    },
    toEmailsVal: {
      hasValidEmails(value) {
        return validEmailsByComma(value);
      },
    },
  },
  methods: {
    onBlur() {
      this.v$.$touch();
      this.$emit('update:bccEmails', this.bccEmailsVal);
      this.$emit('update:ccEmails', this.ccEmailsVal);
      this.$emit('update:toEmails', this.toEmailsVal);
      this.$emit('update:emailSubject', this.emailSubjectVal);
    },
  },
};
</script>

<template>
  <div>
    <div v-if="toEmails || hasHiddenFields" class="input-group-wrap">
      <div
        class="input-group small"
        :class="{ error: toEmails && v$.toEmailsVal.$error }"
      >
        <template v-if="toEmails">
          <label class="input-group-label">
            {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.TO') }}
          </label>
          <div class="flex-1 min-w-0 m-0 rounded-none whitespace-nowrap">
            <woot-input
              v-model="v$.toEmailsVal.$model"
              type="text"
              class="[&>input]:!mb-0 [&>input]:border-transparent [&>input]:!outline-none [&>input]:h-8 [&>input]:!text-sm [&>input]:!border-0 [&>input]:border-none [&>input]:!bg-transparent dark:[&>input]:!bg-transparent"
              :class="{ error: v$.toEmailsVal.$error }"
              :placeholder="
                $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.CC.PLACEHOLDER')
              "
              @blur="onBlur"
            />
          </div>
        </template>
        <div class="flex items-center gap-1 ms-auto shrink-0">
          <ButtonV4
            v-if="!showSubject"
            :label="$t('CONVERSATION.REPLYBOX.EMAIL_HEAD.CUSTOMIZE_SUBJECT')"
            ghost
            xs
            primary
            @click="showSubject = true"
          />
          <ButtonV4
            v-if="!showCc"
            :label="$t('CONVERSATION.REPLYBOX.EMAIL_HEAD.ADD_CC')"
            ghost
            xs
            primary
            @click="showCc = true"
          />
          <ButtonV4
            v-if="!showBcc"
            :label="$t('CONVERSATION.REPLYBOX.EMAIL_HEAD.ADD_BCC')"
            ghost
            xs
            primary
            @click="showBcc = true"
          />
        </div>
      </div>
    </div>
    <div v-if="showCc" class="input-group-wrap">
      <div class="input-group small" :class="{ error: v$.ccEmailsVal.$error }">
        <label class="input-group-label">
          {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.CC.LABEL') }}
        </label>
        <div class="flex-1 min-w-0 m-0 rounded-none whitespace-nowrap">
          <woot-input
            v-model="v$.ccEmailsVal.$model"
            class="[&>input]:!mb-0 [&>input]:border-transparent [&>input]:!outline-none [&>input]:h-8 [&>input]:!text-sm [&>input]:!border-0 [&>input]:border-none [&>input]:!bg-transparent dark:[&>input]:!bg-transparent"
            type="text"
            :class="{ error: v$.ccEmailsVal.$error }"
            :placeholder="$t('CONVERSATION.REPLYBOX.EMAIL_HEAD.CC.PLACEHOLDER')"
            @blur="onBlur"
          />
        </div>
      </div>
      <span v-if="v$.ccEmailsVal.$error" class="message">
        {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.CC.ERROR') }}
      </span>
    </div>
    <div v-if="showBcc" class="input-group-wrap">
      <div class="input-group small" :class="{ error: v$.bccEmailsVal.$error }">
        <label class="input-group-label">
          {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.BCC.LABEL') }}
        </label>
        <div class="flex-1 min-w-0 m-0 rounded-none whitespace-nowrap">
          <woot-input
            v-model="v$.bccEmailsVal.$model"
            type="text"
            class="[&>input]:!mb-0 [&>input]:border-transparent [&>input]:!outline-none [&>input]:h-8 [&>input]:!text-sm [&>input]:!border-0 [&>input]:border-none [&>input]:!bg-transparent dark:[&>input]:!bg-transparent"
            :class="{ error: v$.bccEmailsVal.$error }"
            :placeholder="
              $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.BCC.PLACEHOLDER')
            "
            @blur="onBlur"
          />
        </div>
      </div>
      <span v-if="v$.bccEmailsVal.$error" class="message">
        {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.BCC.ERROR') }}
      </span>
    </div>
    <div v-if="showSubject" class="input-group small">
      <label class="input-group-label">
        {{ $t('CONVERSATION.REPLYBOX.EMAIL_HEAD.SUBJECT') }}
      </label>
      <div class="flex-1 min-w-0 m-0 rounded-none whitespace-nowrap">
        <woot-input
          v-model="emailSubjectVal"
          type="text"
          class="[&>input]:!mb-0 [&>input]:border-transparent [&>input]:!outline-none [&>input]:h-8 [&>input]:!text-sm [&>input]:!border-0 [&>input]:border-none [&>input]:!bg-transparent dark:[&>input]:!bg-transparent"
          @blur="onBlur"
        />
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.input-group-wrap .message {
  @apply text-sm text-n-ruby-8;
}
.input-group {
  @apply border-b border-solid border-n-weak my-1 flex items-center gap-2;

  .input-group-label {
    @apply border-transparent bg-transparent text-xs font-semibold pl-0;
  }
}

.input-group.error {
  @apply border-n-ruby-8;
  .input-group-label {
    @apply text-n-ruby-8;
  }
}
</style>
