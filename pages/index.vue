<template>
  <div class="container">
    <div class="login-box">
      <h2>Login</h2>
      <form @submit.prevent="handleLogin">
        <div class="input-group">
          <span class="icon">👤</span>
          <input v-model="username" type="text" placeholder="Username" required>
        </div>
        <div class="input-group">
          <span class="icon">🔒</span>
          <input v-model="password" type="password" placeholder="Password" required>
        </div>
        <button type="submit" class="btn">Login</button>
        <p class="error" :style="{ color: errorColor }">{{ errorMsg }}</p>
      </form>
    </div>
  </div>
</template>

<script setup>
  import { ref } from 'vue';

  const username = ref('');
  const password = ref('');
  const errorMsg = ref('');
  const errorColor = ref('red');

  function handleLogin() {
    if (username.value === 'admin' && password.value === '1234') {
      errorColor.value = 'green';
      errorMsg.value = '✅ Login realizado com sucesso!';
      setTimeout(() => {
        window.location.href = 'home.html';
      }, 1000);
    } else {
      errorColor.value = 'red';
      errorMsg.value = '❌ Usuário ou senha incorretos!';
    }
  };
</script>

<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: linear-gradient(to bottom, #ffccaa, #ffac75, #ff8431);
    height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100%;
    padding: 20px;
  }

  .login-box {
    background: #fff;
    padding: 30px;
    border-radius: 15px;
    width: 100%;
    max-width: 350px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
    text-align: center;
  }

  .login-box h2 {
    margin-bottom: 20px;
    color: #ff8431;
  }

  .input-group {
    display: flex;
    align-items: center;
    background: #ff833179;
    margin-bottom: 15px;
    border-radius: 25px;
    padding: 10px;
  }

  .input-group .icon {
    margin-right: 10px;
    font-size: 18px;
  }

  .input-group input {
    border: none;
    outline: none;
    background: transparent;
    flex: 1;
    font-size: 14px;
  }

  .btn {
    width: 100%;
    padding: 12px;
    background: #ff8431;
    border: none;
    border-radius: 25px;
    color: white;
    font-size: 16px;
    cursor: pointer;
    transition: 0.3s;
  }

  .btn:hover {
    background: #ff8431;
  }

  .error {
    color: red;
    margin-top: 10px;
    font-size: 14px;
  }

  @media (max-width: 480px) {
    .login-box {
      width: 90%;
      padding: 20px;
    }
  }
</style>