<template>

  <template>
    <UForm :schema="schema" :state="state" class="space-y-4" @submit="onSubmit">
      <UFormGroup label="Email" name="email">
        <UInput v-model="state.email"/>
      </UFormGroup>

      <UFormGroup label="Password" name="password">
        <UInput v-model="state.password" type="password"/>
      </UFormGroup>

      <UButton type="submit">
        Submit
      </UButton>
    </UForm>
  </template>

  <template v-if="isTwoFactorAuthentication">
<!--    <img :src="qrcode" alt="QR Code"/>-->
  </template>

</template>

<script lang="ts" setup>

import {z} from 'zod'


import type {FormSubmitEvent} from "#ui/types";


const schema = z.object({
  email: z.string().email('Invalid email'),
  password: z.string().min(8, 'Must be at least 8 characters')
})

type Schema = z.output<typeof schema>

const state = reactive({
  email: undefined,
  password: undefined
})

async function onSubmit(event: FormSubmitEvent<Schema>) {
  // Do something with data
  console.log(event.data);
  await loginAdministration(event.data.email, event.data.password);

}

const isTwoFactorAuthentication = ref(false);


const msAuth = useMSAuth();

async function login() {
  await msAuth.signIn()
}

const text = ref('');

//const qrcode = useQRCode(text);


async function loginAdministration(username: string, password: string) {
  const url = "http://localhost:5296/auth/mfa/signin";
  try {
    const response = await fetch(url, {
      method: 'POST',
      headers: {
        'Accept': 'text/plain',
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        email: username,
        password: password
      }),
    });

    if (!response.ok) {
      // Handle error response
      const errorData = await response.text();
      console.error('Error:', errorData);
      throw new Error('Failed to login');
    }
    const responseData = await response.json();
    console.log('Success:', responseData);

    if (responseData.message === '2FA setup required') {
      isTwoFactorAuthentication.value = true;
      text.value = responseData.qrCodeUrl;
    }

    return responseData;
  } catch (error) {
    console.error('Error:', error);
    throw error;
  }
}


</script>

<style scoped>

</style>
