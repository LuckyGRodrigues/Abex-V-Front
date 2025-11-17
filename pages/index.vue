<template>
  <div class="container">
    <div class="login-box">
      <h2>Login</h2>
      <form @submit.prevent="handleLogin">
        <div class="input-group">
          <span class="icon">👤</span>
          <input v-model="email" type="email" placeholder="Email" required>
        </div>
        <div class="input-group">
          <span class="icon">🔒</span>
          <input v-model="senha" type="password" placeholder="Senha" required>
        </div>
        <button type="submit" class="btn" :disabled="loading">
          {{ loading ? 'Carregando...' : 'Login' }}
        </button>
        <p class="error" :style="{ color: errorColor }">{{ errorMsg }}</p>
      </form>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      email: '',
      senha: '',
      errorMsg: '',
      errorColor: 'red',
      loading: false,
    };
  },

  methods: {
    async handleLogin() {
      this.loading = true;
      this.errorMsg = '';

      try {
        const response = await this.$api.post('/usuario/check-password', {
          email: this.email,
          senha: this.senha
        });

        if (response.success) {
          this.errorColor = 'green';
          this.errorMsg = '✅ Login realizado com sucesso!';
          
          setTimeout(() => {
            this.$router.push('/inicial');
          }, 1000);
        }
      } catch (error) {
        this.errorColor = 'red';
        
        if (error.response?.status === 404) {
          this.errorMsg = '❌ Usuário não encontrado!';
        } else if (error.response?.status === 401) {
          this.errorMsg = '❌ Senha inválida!';
        } else {
          this.errorMsg = '❌ Erro no servidor. Tente novamente!';
          console.error('Erro no login:', error);
        }
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>

<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #d9e1ed;
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

  .btn:disabled {
    background: #ccc;
    cursor: not-allowed;
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