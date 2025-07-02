<script>
  export let value = '';
  export let idx;
  export let inputRef;
  export let onInput;
  export let onKeyDown;
  export let isLoading = false;
</script>
<input
  type="text"
  bind:this={inputRef}
  value={value}
  on:input={e => onInput(e, idx)}
  on:keydown={e => onKeyDown(e, idx)}
  maxlength="1"
  inputmode="numeric"
  pattern="[0-9]"
  class:filled={value}
  disabled={isLoading}
  autocomplete="one-time-code"
  placeholder="0"
/>

<style>
input {
  width: 48px;
  height: 56px;
  border-radius: 12px;
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
input:focus {
  border: 4px solid #83B1E1;
  color: #69696B;
  background: #fff;
}
input.filled {
  color: #69696B;
}
</style> 