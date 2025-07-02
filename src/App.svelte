<script>
  import flower from './assets/flower.png';
  import flowerCode from './assets/flower code.png';
  import flowerPassword from './assets/flower password.png';
  import celebrar from './assets/celebrar.png';
  import { onMount, tick } from 'svelte';
  let step = 'phone'; // 'phone' | 'code' | 'password'
  let phone = '';
  let isFocused = false;
  let error = false;
  let errorMsg = '';
  $: inputPadding = isFocused ? '70px' : '48px';

  // Для экрана кода
  let code = ['', '', '', '', '', ''];
  let codeInputs = [0, 1, 2, 3, 4, 5];
  let codeRefs = Array(6);
  let codeActiveIdx = 0;

  let password = '';
  let passwordError = '';
  let passwordLoading = false;

  let codeScreenMounted = false;

  function handleSubmit(e) {
    e.preventDefault();
    if (phone.length < 10) {
      error = true;
      errorMsg = 'The phone number must be valid';
      return;
    }
    // Любой 10-значный номер теперь валиден
    error = false;
    errorMsg = '';
    step = 'code';
  }
  function handleInput(e) {
    phone = e.target.value.replace(/\D/g, '').slice(0, 10);
    if (error) {
      error = false;
      errorMsg = '';
    }
  }
  function handleCodeInput(e, idx) {
    let val = e.target.value;
    if (!/^[0-9]?$/.test(val)) {
      val = '';
    }
    code[idx] = val;
    codeRefs[idx]?.focus();
    if (val && idx < 5) {
      codeRefs[idx + 1]?.focus();
    }
    if (val === '' && idx > 0) {
      codeRefs[idx - 1]?.focus();
    }
  }
  function handleCodeKeydown(e, idx) {
    if (e.key === 'Backspace' && !code[idx] && idx > 0) {
      codeRefs[idx - 1]?.focus();
    } else if (e.key === 'ArrowLeft' && idx > 0) {
      codeRefs[idx - 1]?.focus();
      e.preventDefault();
    } else if (e.key === 'ArrowRight' && idx < 5) {
      codeRefs[idx + 1]?.focus();
      e.preventDefault();
    }
  }
  function goBack() {
    if (step === 'code') {
      step = 'phone';
    } else if (step === 'password') {
      step = 'code';
      password = '';
      passwordError = '';
      isFocused = false;
      code = ['', '', '', '', '', ''];
    } else if (step === 'success') {
      step = 'password';
    }
  }

  $: if (step === 'code' && code.join('').length === 6 && code.every(d => d !== '')) {
    step = 'password';
  }

  function handlePasswordInput(e) {
    password = e.target.value;
    if (passwordError) passwordError = '';
  }
  function handlePasswordSubmit(e) {
    e.preventDefault();
    if (password.length < 6) {
      passwordError = 'Пароль должен быть не менее 6 символов';
      return;
    }
    passwordLoading = true;
    setTimeout(() => {
      passwordLoading = false;
      step = 'success';
    }, 1000);
  }

  function goBackToCode() {
    password = '';
    passwordError = '';
    isFocused = false;
    step = 'code';
  }

  $: if (step === 'code' && !codeScreenMounted) {
    codeScreenMounted = true;
    tick().then(() => codeRefs[0]?.focus());
  }
  $: if (step !== 'code' && codeScreenMounted) {
    codeScreenMounted = false;
  }
</script>

{#if step === 'phone'}
<main class="desktop-center">
  <div class="content">
    <img src={flower} alt="flower" class="flower" />
    <h1 class="title">¡Bienvenido a mi proyecto angustiado!</h1>
    <div class="subtitle">Vvedite svoy número de teléfono plz</div>
    <form class="phone-form" on:submit={handleSubmit}>
      <div class="phone-input-wrapper">
        {#if isFocused}
          <span class="country-code">+7</span>
        {/if}
        <input
          class="phone-input {error ? 'error' : ''}"
          type="text"
          inputmode="numeric"
          pattern="[0-9]*"
          placeholder={isFocused ? '' : 'Número de teléfono'}
          bind:value={phone}
          maxlength="10"
          required
          on:input={handleInput}
          on:focus={() => isFocused = true}
          on:blur={() => isFocused = false}
          style="padding-left: {inputPadding};"
        />
      </div>
      {#if error}
        <div class="error-text-under-input">The phone number must be valid</div>
      {/if}
      {#if phone.trim().length > 0}
        <button class="next-btn" type="submit">Далее</button>
        {#if !error}
          <div class="agreement-under-btn">
            Продолжая, вы соглашаетесь получить SMS с кодом, быть счастливым, кайфовать и&nbsp;вкусно кушать
          </div>
        {/if}
      {/if}
    </form>
  </div>
</main>
{/if}

{#if step === 'code'}
<main class="desktop-center">
  <button class="back-arrow" on:click={goBack} aria-label="Назад">
    <svg width="32" height="32" fill="none" stroke="#A9C6EB" stroke-width="2" viewBox="0 0 24 24"><path d="M15 18l-6-6 6-6"/></svg>
  </button>
  <div class="content code-content">
    <img src={flowerCode} alt="flower" class="flower" />
    <h1 class="code-title">Мы отправили тебе код</h1>
    <div class="code-subtitle">Пожалуйста, введи ниже код, который мы (я) отправили тебе (любой)</div>
    <div class="code-inputs" style="position: relative;">
      <input
        class="code-hidden-input"
        type="text"
        inputmode="numeric"
        pattern="[0-9]*"
        maxlength="6"
        autocomplete="one-time-code"
        style="position:absolute;left:0;top:0;width:100%;height:100%;opacity:0;z-index:2;cursor:pointer;"
        on:input={(e) => {
          let val = e.target.value.replace(/[^0-9]/g, '').slice(0, 6);
          for (let i = 0; i < 6; i++) code[i] = val[i] || '';
          if (val.length < 6) e.target.value = val;
        }}
        on:focus={() => codeRefs[0]?.focus()}
      />
      {#each codeInputs as idx}
        <input
          class="code-input {codeActiveIdx === idx ? 'active' : ''}"
          type="text"
          maxlength="1"
          inputmode="numeric"
          pattern="[0-9]*"
          value={code[idx]}
          on:input={(e) => handleCodeInput(e, idx)}
          on:keydown={(e) => handleCodeKeydown(e, idx)}
          this={el => codeRefs[idx] = el}
          on:beforeinput={(e) => { if (e.data && !/^[0-9]$/.test(e.data)) e.preventDefault(); }}
          on:paste={(e) => { const text = (e.clipboardData || window.clipboardData).getData('text'); if (!/^\d+$/.test(text)) e.preventDefault(); }}
          on:mousedown={() => codeActiveIdx = idx}
          on:focus={() => { codeActiveIdx = idx; document.querySelector('.code-hidden-input')?.focus(); }}
        />
        {#if idx === 2}
          <span class="code-dash">—</span>
        {/if}
      {/each}
    </div>
    <div class="code-bottom-text">
      Meow &nbsp;•&nbsp; Chmok &nbsp;•&nbsp; Chpon'k
    </div>
  </div>
</main>
{/if}

{#if step === 'password'}
<main class="desktop-center">
  <button class="back-arrow" on:click={goBack} aria-label="Назад">
    <svg width="32" height="32" fill="none" stroke="#A9C6EB" stroke-width="2" viewBox="0 0 24 24"><path d="M15 18l-6-6 6-6"/></svg>
  </button>
  <div class="content">
    <img src={flowerPassword} alt="flower" class="flower" />
    <h1 class="title password-title">Супер! Введите, пж ваш пароль</h1>
    <form class="phone-form" on:submit={handlePasswordSubmit}>
      <div class="phone-input-wrapper">
        <input
          class="phone-input password-active {passwordError ? 'error' : ''}"
          type="password"
          placeholder={isFocused ? '' : 'Tu contraseña'}
          bind:value={password}
          minlength="6"
          required
          on:input={handlePasswordInput}
          on:focus={() => isFocused = true}
          on:blur={() => isFocused = false}
          style="padding-left: {inputPadding};"
          disabled={passwordLoading}
        />
      </div>
      {#if passwordError}
        <div class="error-text-under-input">{passwordError}</div>
      {/if}
      {#if password.trim().length > 0}
        <button class="next-btn" type="submit" disabled={password.length < 6 || passwordLoading}>
          {#if passwordLoading}
            <span class="btn-loader"></span>
          {:else}
            Continue
          {/if}
        </button>
      {/if}
    </form>
  </div>
</main>
{/if}

{#if step === 'success'}
<main class="desktop-center">
  <button class="back-arrow" on:click={goBack} aria-label="Назад">
    <svg width="32" height="32" fill="none" stroke="#A9C6EB" stroke-width="2" viewBox="0 0 24 24"><path d="M15 18l-6-6 6-6"/></svg>
  </button>
  <div class="content">
    <img src={celebrar} alt="celebration" class="flower flower-success" />
    <h1 class="title">Добро пожаловать на сервер шизофрения!</h1>
  </div>
</main>
{/if}

<style>
.desktop-center {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
.content {
  width: 782px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.flower {
  width: 180px;
  height: 180px;
  margin-bottom: 40px;
}
.title {
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 600;
  font-size: 28px;
  line-height: 100%;
  color: #69696B;
  margin-bottom: 16px;
  text-align: center;
}
.subtitle {
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  font-size: 24px;
  line-height: 100%;
  color: #C8C8CA;
  margin-bottom: 48px;
  text-align: center;
}
.phone-form {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 56px;
  margin-bottom: 48px;
}
.phone-input-wrapper {
  position: relative;
  width: 600px;
  height: 88px;
  display: flex;
  align-items: center;
  margin-bottom: 0;
}
.country-code {
  position: absolute;
  left: 32px;
  color: #69696B;
  font-size: 24px;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  z-index: 2;
  pointer-events: none;
  user-select: none;
}
.phone-input {
  width: 600px;
  height: 88px;
  background: #F8F8F8;
  border: none;
  border-radius: 24px;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  font-size: 24px;
  line-height: 100%;
  color: #C8C8CA;
  padding: 0 32px 0 32px;
  outline: none;
  box-sizing: border-box;
  transition: border 0.2s, color 0.2s, padding 0.2s;
}
.phone-input.error {
  border-radius: 32px;
  border: 4px solid #E95C28;
  background: #F8F8F8;
  color: #E95C28;
}
.phone-input:focus {
  color: #69696B;
  border: 4px solid #83B1E1;
  background: #F8F8F8;
}
.phone-input.error:focus {
  border: 4px solid #E95C28;
  color: #E95C28;
  background: #F8F8F8;
}
.phone-input::placeholder {
  color: #C8C8CA;
  opacity: 1;
}
.error-text-under-input {
  margin-top: 16px;
  color: #E95C28;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-size: 20px;
  text-align: center;
  width: 600px;
  margin-left: 0;
}
.next-btn {
  width: 600px;
  height: 72px;
  background: #A9C6EB;
  border: none;
  border-radius: 24px;
  color: #fff;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 600;
  font-size: 28px;
  cursor: pointer;
  transition: background 0.2s, box-shadow 0.2s;
}
.next-btn:hover {
  background: #8bb3db;
  box-shadow: 0px 4px 6px 0px rgba(152, 152, 152, 0.30);
}
.next-btn:active {
  box-shadow: 0px 4px 4px 0px rgba(152, 152, 152, 0.25) inset;
}
.agreement-under-btn {
  width: 600px;
  margin-top: 0;
  color: #C8C8CA;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-size: 24px;
  text-align: center;
  max-width: 900px;
  margin-left: auto;
  margin-right: auto;
}
.back-arrow {
  position: absolute;
  top: 32px;
  left: 32px;
  background: none;
  border: none;
  cursor: pointer;
  z-index: 10;
  padding: 0;
}
.back-arrow svg {
  stroke: #83B1E1;
  transition: stroke 0.2s;
}
.back-arrow:hover svg {
  stroke: #679CD2;
}
.code-content {
  margin-top: 0;
  position: relative;
  min-height: 100vh;
}
.code-title {
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 600;
  font-size: 28px;
  line-height: 100%;
  color: #69696B;
  margin-top: 24px;
  margin-bottom: 16px;
  text-align: center;
}
.code-subtitle {
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  font-size: 24px;
  line-height: 100%;
  color: #C8C8CA;
  margin-bottom: 48px;
  text-align: center;
}
.code-inputs {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 24px;
  margin-bottom: 48px;
}
.code-input {
  width: 88px;
  height: 88px;
  border-radius: 24px;
  background: #F8F8F8;
  border: none;
  font-size: 32px;
  color: #C8C8CA;
  text-align: center;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  outline: none;
  transition: border 0.2s, color 0.2s;
}
.code-input.active,
.code-input:focus {
  border: 4px solid #83B1E1;
  color: #69696B;
  background: #fff;
}
.code-input:not(:placeholder-shown) {
  color: #69696B;
}
.code-dash {
  font-size: 40px;
  color: #E5E5E5;
  margin: 0 12px;
  user-select: none;
}
.code-bottom-text {
  position: absolute;
  left: 0;
  bottom: 64px;
  width: 100%;
  color: #C8C8CA;
  font-size: 20px;
  text-align: center;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
}
.password-form {
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 32px;
  margin-bottom: 48px;
}
.password-input {
  width: 600px;
  height: 88px;
  background: #F8F8F8;
  border: none;
  border-radius: 24px;
  font-family: "SF Pro Display", "SF Pro Text", -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Arial", sans-serif;
  font-weight: 500;
  font-size: 24px;
  line-height: 100%;
  color: #C8C8CA;
  padding: 0 32px;
  outline: none;
  box-sizing: border-box;
  transition: border 0.2s, color 0.2s;
}
.password-input:focus {
  color: #69696B;
  border: 4px solid #83B1E1;
  background: #F8F8F8;
}
.password-input.error {
  border-radius: 32px;
  border: 4px solid #E95C28;
  background: #F8F8F8;
}
.password-title {
  margin-bottom: 56px;
}
.flower-success {
  width: 364px;
  height: 364px;
}
.phone-input.password-active:focus {
  padding-left: 32px;
}
.btn-loader {
  display: inline-block;
  width: 24px;
  height: 24px;
  border: 4px solid rgba(255,255,255,0.3);
  border-top: 4px solid #fff;
  border-radius: 50%;
  margin-right: 0;
  vertical-align: middle;
  animation: btn-spin 1s linear infinite;
}
@keyframes btn-spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
.code-hidden-input {
  position: absolute;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  opacity: 0;
  z-index: 2;
  cursor: pointer;
}
</style>