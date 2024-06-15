<script setup lang="ts">
import { useRouter } from 'vue-router';
import { ref } from 'vue';
import { Auth } from '../auth';

const router = useRouter();
const email = ref('');
const password = ref('');
const remember = ref(false);
const awaiting = ref(false);

function onSubmit() {
  awaiting.value = true;
  const auth = new Auth(remember.value);
  auth.signIn(email.value || '', password.value || '', () => {
    awaiting.value = false;
    router.push('/');
  }, () => {
    awaiting.value = false;
    console.log('Login failed!');
  });
}
</script>

<template>
  <div class="login-container flex">

<div class="login-form">
    <h2>Bem vindo, Lojista!</h2>
    <form action="#" method="POST">
        <label for="username">Usuário</label>
        <input type="text" id="username" name="username" required>
        <label for="password">Senha</label>
        <input type="password" id="password" name="password" required>
        <button type="submit">Entrar</button>
        <p class="signup">Não tem uma conta? <a href="/signinadmin">Solicite uma agora</a></p>
    </form>
</div>

<div class="login-image">
  <img src="../../../assets/login-admin1.jpg" alt="Godzilla eating">
</div>

</div>

</template>

<style>
.login-container {
  background-color: rgba(255, 166, 0, 0.199);
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.login-image, .login-form {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
}

.login-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.login-form {
  height: 100vh;
  flex: 1;
  flex-direction: column;
  padding: 40px;
}

.login-form h2 {
  margin-bottom: 20px;
  font-size: 2em;
  color: #333;
}

.login-form form {
  width: 100%;
  max-width: 350px;
}

.login-form label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}

.login-form input {
  width: 100%;
  padding: 10px;
  margin-bottom: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.login-form button {
  width: 100%;
  padding: 10px;
  background-color: var(--red);
  color: #fff;
  border: none;
  border-radius: 5px;
  font-size: 1em;
  cursor: pointer;
}

.login-form button:hover {
  background-color: var(--orange);
}

.signup {
  margin-top: 20px;
  text-align: center;
}

.signup a {
  color: var(--red);
  text-decoration: none;
}

.signup a:hover {
  text-decoration: underline;
}


@media (max-width: 650px) {
  .login-image {
    display: none;
  }
}

</style>
