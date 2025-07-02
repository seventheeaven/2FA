<script>
  import { createEventDispatcher } from 'svelte';
  
  const dispatch = createEventDispatcher();
  
  let password = '';
  let showPassword = false;
  let isLoading = false;

  function handleSubmit(event) {
    event.preventDefault();
    if (password.length >= 6) {
      isLoading = true;
      // Симуляция проверки пароля
      setTimeout(() => {
        dispatch('submit', password);
        isLoading = false;
      }, 1000);
    }
  }

  function handleBack() {
    dispatch('back');
  }

  function togglePassword() {
    showPassword = !showPassword;
  }
</script>

<div class="step">
  <div class="step-icon">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
      <rect x="3" y="11" width="18" height="11" rx="2" ry="2"/>
      <circle cx="12" cy="16" r="1"/>
      <path d="M7 11V7a5 5 0 0 1 10 0v4"/>
    </svg>
  </div>
  
  <h2>Введите пароль</h2>
  <p>Для завершения входа введите ваш пароль</p>

  <form on:submit={handleSubmit}>
    <div class="input-group">
      <div class="password-input-wrapper">
        <input
          type={showPassword ? 'text' : 'password'}
          value={password}
          on:input={(e) => password = e.target.value}
          placeholder="Введите пароль"
          required
          disabled={isLoading}
          autocomplete="current-password"
        />
        <button 
          type="button" 
          class="toggle-password" 
          on:click={togglePassword}
          disabled={isLoading}
        >
          {#if showPassword}
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M17.94 17.94A10.07 10.07 0 0 1 12 20c-7 0-11-8-11-8a18.45 18.45 0 0 1 5.06-5.94M9.9 4.24A9.12 9.12 0 0 1 12 4c7 0 11 8 11 8a18.5 18.5 0 0 1-2.16 3.19m-6.72-1.07a3 3 0 1 1-4.24-4.24"/>
              <line x1="1" y1="1" x2="23" y2="23"/>
            </svg>
          {:else}
            <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
              <path d="M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z"/>
              <circle cx="12" cy="12" r="3"/>
            </svg>
          {/if}
        </button>
      </div>
      <div class="password-hint">Минимум 6 символов</div>
    </div>
    
    <button type="submit" disabled={password.length < 6 || isLoading}>
      {#if isLoading}
        <span class="spinner"></span>
        Проверка...
      {:else}
        Войти
      {/if}
    </button>
  </form>

  <button class="back-btn" on:click={handleBack}>
    ← Назад к коду
  </button>
</div>

<style>
  .step {
    text-align: center;
  }

  .step-icon {
    width: 64px;
    height: 64px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 24px;
    color: white;
  }

  h2 {
    color: #1a202c;
    font-size: 20px;
    font-weight: 600;
    margin-bottom: 8px;
  }

  p {
    color: #718096;
    font-size: 14px;
    margin-bottom: 32px;
  }

  .input-group {
    margin-bottom: 24px;
  }

  .password-input-wrapper {
    position: relative;
  }

  input {
    width: 100%;
    padding: 16px;
    padding-right: 48px;
    border: 2px solid #e2e8f0;
    border-radius: 12px;
    font-size: 16px;
    transition: all 0.2s;
    background: #f7fafc;
  }

  input:focus {
    outline: none;
    border-color: #667eea;
    background: white;
    box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
  }

  input:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .toggle-password {
    position: absolute;
    right: 12px;
    top: 50%;
    transform: translateY(-50%);
    background: none;
    border: none;
    color: #a0aec0;
    cursor: pointer;
    padding: 4px;
    border-radius: 4px;
    transition: all 0.2s;
    width: auto;
  }

  .toggle-password:hover:not(:disabled) {
    color: #667eea;
    background: rgba(102, 126, 234, 0.1);
    transform: translateY(-50%);
  }

  .toggle-password:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .password-hint {
    font-size: 12px;
    color: #a0aec0;
    margin-top: 8px;
  }

  button[type="submit"] {
    width: 100%;
    padding: 16px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 12px;
    font-size: 16px;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.2s;
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
  }

  button[type="submit"]:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
  }

  button[type="submit"]:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    transform: none;
  }

  .back-btn {
    background: transparent;
    color: #718096;
    border: none;
    padding: 12px;
    font-size: 14px;
    text-decoration: underline;
    width: auto;
    margin-top: 16px;
  }

  .back-btn:hover {
    color: #4a5568;
    transform: none;
    box-shadow: none;
  }

  .spinner {
    width: 16px;
    height: 16px;
    border: 2px solid transparent;
    border-top: 2px solid white;
    border-radius: 50%;
    animation: spin 1s linear infinite;
  }

  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }
</style> 