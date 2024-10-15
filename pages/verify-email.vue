<script setup lang="ts">
import { ref } from 'vue'

definePageMeta({ middleware: ["unverified"] });

const { logout, resendEmailVerification } = useAuth();
const verificationStatus = ref<'idle' | 'sent' | 'error'>('idle');

async function handleResendVerification() {
  try {
    verificationStatus.value = 'idle';
    const { status } = await resendEmailVerification();
    if (status === "verification-link-sent") {
      verificationStatus.value = 'sent';
    } else {
      throw new Error('Falha ao enviar o e-mail de verificação');
    }
  } catch (error) {
    console.error('Erro ao reenviar e-mail de verificação:', error);
    verificationStatus.value = 'error';
  }
}
</script>

<template>
  <AuthCard>
    <template #logo>
      <NuxtLink to="/">
        <ApplicationLogo class="w-20 h-20 text-gray-500 fill-current" />
      </NuxtLink>
    </template>

    <p class="mb-4 text-sm text-gray-600">
      Obrigado por se cadastrar! Antes de começar, confirme seu e-mail clicando no link que acabamos de te enviar. 
      Caso não tenha recebido, podemos enviar novamente.
    </p>

    <p v-if="verificationStatus === 'sent'" class="mb-4 text-sm font-medium text-green-600">
      Um novo link de verificação foi enviado para o seu endereço de e-mail.
    </p>

    <p v-if="verificationStatus === 'error'" class="mb-4 text-sm font-medium text-red-600">
      Ocorreu um erro ao enviar o e-mail de verificação. Por favor, tente novamente.
    </p>

    <div class="flex items-center justify-between mt-4">
      <Button @click="handleResendVerification" :disabled="verificationStatus === 'sent'">
        {{ verificationStatus === 'sent' ? 'E-mail enviado' : 'Reenviar e-mail de verificação' }}
      </Button>
      <button type="button" class="text-sm text-gray-600 underline hover:text-gray-900" @click="logout">
        Logout
      </button>
    </div>
  </AuthCard>
</template>
