<script>
  import { createEventDispatcher } from 'svelte';
  import CodeInput from './CodeInput.svelte';
  
  const dispatch = createEventDispatcher();
  export let phone = '';
  
  let code = ['', '', '', '', '', ''];
  let isLoading = false;
  let timeLeft = 60;
  let canResend = false;
  let codeRefs = Array(6);

  async function handleCodeInput(e, idx) {
    const val = e.target.value.replace(/\D/g, '').slice(0, 1);
    code[idx] = val;
    await tick();
    if (val && idx < 5) {
      codeRefs[idx + 1]?.focus();
    }
    if (val === '' && idx > 0) {
      codeRefs[idx - 1]?.focus();
    }
    // Если все поля заполнены, отправляем форму автоматически
    if (code.join('').length === 6 && code.every(d => d !== '')) {
      handleSubmit(new Event('submit'));
    }
  }

  function handleKeyDown(e, idx) {
    if (e.key === 'Backspace' && code[idx] === '' && idx > 0) {
      codeRefs[idx - 1]?.focus();
    }
  }

  function handleSubmit(event) {
    event.preventDefault();
    const codeStr = code.join('');
    if (codeStr.length === 6) {
      isLoading = true;
      setTimeout(() => {
        dispatch('submit', codeStr);
        isLoading = false;
      }, 1000);
    }
  }

  function handleBack() {
    dispatch('back');
  }

  function resendCode() {
    if (canResend) {
      timeLeft = 60;
      canResend = false;
      startTimer();
      console.log('Новый код отправлен');
    }
  }

  function formatPhone(phone) {
    return phone.replace(/(\+7\s\(\d{3}\)\s\d{3}-\d{2}-\d{2})/, (match, p1) => {
      return p1.replace(/\d/g, '*');
    });
  }

  // Таймер для повторной отправки
  function startTimer() {
    const timer = setInterval(() => {
      timeLeft--;
      if (timeLeft <= 0) {
        clearInterval(timer);
        canResend = true;
      }
    }, 1000);
  }

  startTimer();
</script>

<!-- Вынесенный компонент для одной цифры -->
<script context="module">
  export let digit;
  export let idx;
  export let codeRefs;
  export let handleCodeInput;
  export let handleKeyDown;
  export let isLoading;
</script>

<div class="step">
  <div class="step-icon">
    <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
      <path d="M12 2L2 7l10 5 10-5-10-5zM2 17l10 5 10-5M2 12l10 5 10-5"/>
    </svg>
  </div>
  
  <div class="header-block">
    <h2>Введите код из SMS</h2>
    <p>Код отправлен на номер {formatPhone(phone)}</p>
  </div>

  <form on:submit={handleSubmit}>
    <div class="input-group code-inputs">
      {#each code as digit, idx}
        <CodeInput
          bind:value={code[idx]}
          {idx}
          inputRef={codeRefs[idx]}
          onInput={handleCodeInput}
          onKeyDown={handleKeyDown}
          {isLoading}
        />
      {/each}
    </div>
    <div class="code-hint">Введите 6-значный код</div>
    <button type="submit" disabled={code.join('').length !== 6 || isLoading}>
      {#if isLoading}
        <span class="spinner"></span>
        Проверка...
      {:else}
        Подтвердить
      {/if}
    </button>
  </form>

  <div class="resend-section">
    {#if canResend}
      <button class="resend-btn" on:click={resendCode}>
        Отправить код повторно
      </button>
    {:else}
      <p class="timer">
        Отправить код повторно через {timeLeft} сек
      </p>
    {/if}
  </div>

  <button class="back-btn" on:click={handleBack}>
    ← Изменить номер
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

  .header-block {
    min-height: 64px;
    margin-bottom: 0;
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
  }
  .header-block h2 {
    color: #1a202c;
    font-size: 20px;
    font-weight: 600;
    margin-bottom: 8px;
  }
  .header-block p {
    color: #718096;
    font-size: 14px;
    margin-bottom: 32px;
  }

  h2, p {
    margin-bottom: 0;
  }

  .input-group {
    margin-bottom: 24px;
  }

  .code-inputs {
    display: flex;
    gap: 16px;
    justify-content: center;
    margin-bottom: 8px;
  }
  .code-hint {
    font-size: 12px;
    color: #a0aec0;
    margin-top: 8px;
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

  .resend-section {
    margin: 24px 0;
  }

  .resend-btn {
    background: transparent;
    color: #667eea;
    border: 2px solid #667eea;
    padding: 12px;
    font-size: 14px;
  }

  .resend-btn:hover {
    background: #667eea;
    color: white;
  }

  .timer {
    font-size: 14px;
    color: #a0aec0;
  }

  .back-btn {
    background: transparent;
    color: #718096;
    border: none;
    padding: 12px;
    font-size: 14px;
    text-decoration: underline;
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