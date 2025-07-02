<script>
  import { createEventDispatcher } from 'svelte';
  
  const dispatch = createEventDispatcher();
  let phone = '';
  let isLoading = false;

  function handleSubmit(event) {
    event.preventDefault();
    if (phone.length >= 10) {
      isLoading = true;
      // Симуляция задержки отправки SMS
      setTimeout(() => {
        dispatch('submit', phone);
        isLoading = false;
      }, 1000);
    }
  }

  function formatPhone(value) {
    // Удаляем все нецифровые символы
    const cleaned = value.replace(/\D/g, '');
    
    // Форматируем номер телефона
    if (cleaned.length <= 1) {
      return cleaned;
    } else if (cleaned.length <= 4) {
      return `+7 (${cleaned.slice(1, 4)}`;
    } else if (cleaned.length <= 7) {
      return `+7 (${cleaned.slice(1, 4)}) ${cleaned.slice(4, 7)}`;
    } else if (cleaned.length <= 9) {
      return `+7 (${cleaned.slice(1, 4)}) ${cleaned.slice(4, 7)}-${cleaned.slice(7, 9)}`;
    } else {
      return `+7 (${cleaned.slice(1, 4)}) ${cleaned.slice(4, 7)}-${cleaned.slice(7, 9)}-${cleaned.slice(9, 11)}`;
    }
  }
</script>

<div class="step">
  <div class="step-icon">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
      <path d="M22 16.92v3a2 2 0 0 1-2.18 2 19.79 19.79 0 0 1-8.63-3.07 19.5 19.5 0 0 1-6-6 19.79 19.79 0 0 1-3.07-8.67A2 2 0 0 1 4.11 2h3a2 2 0 0 1 2 1.72 12.84 12.84 0 0 0 .7 2.81 2 2 0 0 1-.45 2.11L8.09 9.91a16 16 0 0 0 6 6l1.27-1.27a2 2 0 0 1 2.11-.45 12.84 12.84 0 0 0 2.81.7A2 2 0 0 1 22 16.92z"/>
    </svg>
  </div>
  
  <h2>Введите номер телефона</h2>
  <p>Мы отправим код подтверждения на ваш номер</p>

  <form on:submit={handleSubmit}>
    <div class="input-group">
      <input
        type="tel"
        bind:value={phone}
        on:input={(e) => phone = formatPhone(e.target.value)}
        placeholder="+7 (___) ___-__-__"
        maxlength="18"
        required
        disabled={isLoading}
      />
    </div>
    
    <button type="submit" disabled={phone.length < 18 || isLoading}>
      {#if isLoading}
        <span class="spinner"></span>
        Отправка...
      {:else}
        Отправить код
      {/if}
    </button>
  </form>
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

  input {
    width: 100%;
    padding: 16px;
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

  button {
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

  button:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
  }

  button:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    transform: none;
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