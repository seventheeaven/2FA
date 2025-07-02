<script>
  export let phone = '';
  
  function formatPhone(phone) {
    return phone.replace(/(\+7\s\(\d{3}\)\s\d{3}-\d{2}-\d{2})/, (match, p1) => {
      return p1.replace(/\d/g, '*');
    });
  }
</script>

<div class="step">
  <div class="success-icon">
    <svg width="32" height="32" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
      <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/>
      <polyline points="22,4 12,14.01 9,11.01"/>
    </svg>
  </div>
  
  <h2>Добро пожаловать!</h2>
  <p>Вы успешно вошли в систему</p>

  <div class="user-info">
    <div class="info-item">
      <span class="label">Номер телефона:</span>
      <span class="value">{formatPhone(phone)}</span>
    </div>
    <div class="info-item">
      <span class="label">Время входа:</span>
      <span class="value">{new Date().toLocaleString('ru-RU')}</span>
    </div>
  </div>

  <div class="success-message">
    <p>✅ Двухфакторная аутентификация пройдена</p>
    <p>✅ Пароль подтвержден</p>
    <p>✅ Доступ к системе получен</p>
  </div>

  <div class="actions">
    <button class="primary-btn" on:click={() => window.location.reload()}>
      Выйти и начать заново
    </button>
  </div>
</div>

<style>
  .step {
    text-align: center;
  }

  .success-icon {
    width: 80px;
    height: 80px;
    background: linear-gradient(135deg, #48bb78 0%, #38a169 100%);
    border-radius: 50%;
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 0 auto 24px;
    color: white;
    animation: scaleIn 0.5s ease-out;
  }

  @keyframes scaleIn {
    0% {
      transform: scale(0);
      opacity: 0;
    }
    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

  h2 {
    color: #1a202c;
    font-size: 24px;
    font-weight: 600;
    margin-bottom: 8px;
  }

  p {
    color: #718096;
    font-size: 16px;
    margin-bottom: 32px;
  }

  .user-info {
    background: #f7fafc;
    border-radius: 12px;
    padding: 20px;
    margin-bottom: 24px;
    text-align: left;
  }

  .info-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 8px 0;
    border-bottom: 1px solid #e2e8f0;
  }

  .info-item:last-child {
    border-bottom: none;
  }

  .label {
    color: #718096;
    font-size: 14px;
    font-weight: 500;
  }

  .value {
    color: #1a202c;
    font-size: 14px;
    font-weight: 600;
  }

  .success-message {
    background: linear-gradient(135deg, #c6f6d5 0%, #9ae6b4 100%);
    border-radius: 12px;
    padding: 20px;
    margin-bottom: 32px;
  }

  .success-message p {
    color: #22543d;
    font-size: 14px;
    margin-bottom: 8px;
    font-weight: 500;
  }

  .success-message p:last-child {
    margin-bottom: 0;
  }

  .actions {
    margin-top: 24px;
  }

  .primary-btn {
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
  }

  .primary-btn:hover {
    transform: translateY(-2px);
    box-shadow: 0 8px 25px rgba(102, 126, 234, 0.3);
  }
</style> 