<script setup lang="ts">
import { useRouter } from 'vue-router';
import { ref } from 'vue';
import { Auth } from '../auth';

const router = useRouter();
const email = ref('');
const password = ref('');
const password_confirmation = ref('')
const remember = ref(false);
const awaiting = ref(false);

function onSubmit() {
  awaiting.value = true;
  const auth = new Auth(remember.value);
  auth.signUp(email.value || '', password.value || '', password_confirmation.value || '', () => {
    awaiting.value = false;
    router.push('/stores');
  }, () => {
    awaiting.value = false;
    console.log('Sign Up failed!');
  });
}
</script>

<template>
<div class="register-container flex">
  
  <div class="register-image">
    <img src="../assets/imgs/login-admin2.jpg" alt="Godzilla eating">
  </div>
  
  <div class="register-form">
        <h2>Crie sua Conta</h2>

        <form @submit.prevent="onSubmit">
                    
          <label for="email">Email</label>
          <input v-model="email" type="email" id="email" name="email" required>

          <label for="password">Senha</label>
          <input v-model="password" type="password" name="password" required>
          
          <label for="password">Confirme a senha</label>
          <input v-model="password_confirmation" type="password" name="password" required>
          
          <div class="remember">
            <input v-model="remember" type="checkbox"><br />
            <label>Lembrar minha senha </label>
          </div>

          <button type="submit" v-show="!awaiting">Cadastrar</button>
          <p class="login">Já tem uma conta? <a href="/signin">Faça login</a></p>
      </form>
    </div>


</div>

</template>

<style>.register-container {
  background-color: rgba(255, 166, 0, 0.199);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.register-image, .register-form {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.note {
  margin-bottom: 70px;
}

.register-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.register-form {
  height: 100vh;
  flex: 1;
  flex-direction: column;
  padding: 40px;
}

.register-form h2 {
  margin-bottom: 20px;
  font-size: 2em;
  color: #333;
}

.register-form form {
  width: 100%;
  max-width: 400px;
}

.register-form label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

.register-form input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.register-form button {
  width: 100%;
  padding: 10px;
  background-color: var(--red);
  color: #fff;
  border: none;
  border-radius: 5px;
  font-size: 1em;
  cursor: pointer;
}

.register-form button:hover {
  background-color: var(--orange);
}

.login {
  margin-top: 20px;
  text-align: center;
}

.login a {
  color: var(--red);
  text-decoration: none;
}

.login a:hover {
  text-decoration: underline;
}

@media (max-width: 650px) {
  .register-image {
    display: none;
  }
}

</style>
