<script>
  import { supabase } from './supabase.js';

  let email = '';
  let password = '';
  let isSignUp = false;
  let error = '';
  let loading = false;

  async function handleSubmit() {
    error = '';
    loading = true;

    if (!email || !password) {
      error = 'Email and password are required';
      loading = false;
      return;
    }

    try {
      if (isSignUp) {
        const { error: signUpError } = await supabase.auth.signUp({
          email,
          password
        });
        if (signUpError) throw signUpError;
        error = 'Check your email to confirm your account';
      } else {
        const { error: signInError } = await supabase.auth.signInWithPassword({
          email,
          password
        });
        if (signInError) throw signInError;
      }
    } catch (e) {
      error = e.message || 'Authentication failed';
    }

    loading = false;
  }

  function toggleMode() {
    isSignUp = !isSignUp;
    error = '';
  }

  async function demoLogin() {
    email = 'test@test.com';
    password = 'test';
    isSignUp = false;
    error = '';
    loading = true;

    try {
      const { error: signInError } = await supabase.auth.signInWithPassword({
        email: 'test@test.com',
        password: 'test'
      });
      if (signInError) throw signInError;
    } catch (e) {
      error = e.message || 'Authentication failed';
      loading = false;
    }
  }
</script>

<div class="auth-container">
  <!-- Background Gradient -->
  <div class="bg-gradient"></div>

  <!-- Content -->
  <div class="content-wrapper">
    <!-- Header -->
    <div class="header">
      <div class="logo-brand">
        <img src="/logo.png" alt="TaskPulse Poppins" class="logo" />
        <div class="brand-text">
          <h1>TaskPulse</h1>
          <p>Poppins</p>
        </div>
      </div>
    </div>

    <!-- Main Grid -->
    <div class="main-grid">
      <!-- Features Section -->
      <div class="features-section">
        <h2>Master Your Productivity</h2>
        <p class="subtitle">All the tools you need to organize, prioritize, and track your tasks</p>

        <div class="features-grid">
          <div class="feature-card">
            <div class="feature-icon">✓</div>
            <h3>Organize Tasks</h3>
            <p>Create multiple lists and organize by project or category</p>
          </div>

          <div class="feature-card">
            <div class="feature-icon">☁</div>
            <h3>Cloud Sync</h3>
            <p>Seamless sync across all your devices</p>
          </div>

          <div class="feature-card">
            <div class="feature-icon">⚡</div>
            <h3>Priority Levels</h3>
            <p>Mark tasks as high, medium, or low priority</p>
          </div>

          <div class="feature-card">
            <div class="feature-icon">📅</div>
            <h3>Due Dates</h3>
            <p>Set deadlines and never miss a task</p>
          </div>

          <div class="feature-card">
            <div class="feature-icon">🔒</div>
            <h3>Secure & Private</h3>
            <p>Your data is encrypted and private</p>
          </div>

          <div class="feature-card">
            <div class="feature-icon">🎨</div>
            <h3>Dark Mode</h3>
            <p>Choose between light and dark themes</p>
          </div>
        </div>
      </div>

      <!-- Form Section -->
      <div class="form-section">
        <div class="form-card">
          <div class="form-header">
            <div class="header-top">
              <div>
                <h2>{isSignUp ? 'Create Your Account' : 'Welcome Back'}</h2>
                <p>
                  {isSignUp
                    ? 'Join and start organizing your tasks'
                    : 'Sign in to your task list'}
                </p>
              </div>
              {#if !isSignUp}
                <button type="button" class="demo-btn" on:click={demoLogin} disabled={loading}>
                  🚀 DEMO
                </button>
              {/if}
            </div>
          </div>

          <form on:submit|preventDefault={handleSubmit}>
            <div class="input-group">
              <label for="email">Email Address</label>
              <input
                id="email"
                type="email"
                placeholder="you@example.com"
                bind:value={email}
                disabled={loading}
                required
              />
            </div>

            <div class="input-group">
              <label for="password">Password</label>
              <input
                id="password"
                type="password"
                placeholder="Enter your password"
                bind:value={password}
                disabled={loading}
                required
              />
            </div>

            {#if error}
              <div class="error-box">{error}</div>
            {/if}

            <button type="submit" disabled={loading} class="submit-btn">
              {loading ? 'Loading...' : isSignUp ? 'Create Account' : 'Sign In'}
            </button>
          </form>

          <div class="divider">or</div>

          <div class="toggle-section">
            <p>
              {isSignUp ? 'Already have an account?' : "Don't have an account?"}
            </p>
            <button
              type="button"
              on:click={toggleMode}
              disabled={loading}
              class="toggle-btn"
            >
              {isSignUp ? 'Sign In Instead' : 'Create One'}
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</div>

<style>
  .auth-container {
    position: relative;
    width: 100%;
    min-height: 100vh;
    overflow: hidden;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  }

  .bg-gradient {
    display: none;
  }

  .content-wrapper {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
    padding: 40px 20px;
  }

  /* Header */
  .header {
    text-align: center;
    margin-bottom: 50px;
    color: white;
  }

  .logo-brand {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 16px;
  }

  .logo {
    height: 60px;
    object-fit: contain;
  }

  .brand-text h1 {
    font-size: 2.2rem;
    font-weight: 700;
    margin: 0;
    line-height: 1;
  }

  .brand-text p {
    font-size: 0.95rem;
    margin: 4px 0 0 0;
    opacity: 0.9;
    font-weight: 500;
  }

  /* Main Grid */
  .main-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 60px;
    align-items: center;
    max-width: 1200px;
    margin: 0 auto;
    width: 100%;
    flex: 1;
  }

  /* Features Section */
  .features-section {
    color: white;
  }

  .features-section h2 {
    font-size: 2.8rem;
    font-weight: 700;
    margin: 0 0 16px 0;
    line-height: 1.2;
  }

  .subtitle {
    font-size: 1.1rem;
    opacity: 0.95;
    margin: 0 0 40px 0;
    line-height: 1.6;
    max-width: 500px;
  }

  .features-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }

  .feature-card {
    background: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    padding: 24px;
    border-radius: 12px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    transition: all 0.3s ease;
  }

  .feature-card:hover {
    background: rgba(255, 255, 255, 0.15);
    transform: translateY(-4px);
    border-color: rgba(255, 255, 255, 0.3);
  }

  .feature-icon {
    font-size: 2.2rem;
    margin-bottom: 12px;
    display: block;
  }

  .feature-card h3 {
    font-size: 1rem;
    font-weight: 600;
    margin: 0 0 8px 0;
  }

  .feature-card p {
    font-size: 0.9rem;
    margin: 0;
    opacity: 0.9;
    line-height: 1.5;
  }

  /* Form Section */
  .form-section {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .form-card {
    background: white;
    border-radius: 16px;
    padding: 40px;
    width: 100%;
    max-width: 420px;
    box-shadow: 0 20px 60px rgba(0, 0, 0, 0.15);
  }

  .form-header {
    margin-bottom: 32px;
  }

  .form-header h2 {
    font-size: 1.8rem;
    font-weight: 700;
    color: #1a1a2e;
    margin: 0 0 12px 0;
  }

  .form-header p {
    font-size: 0.95rem;
    color: #888;
    margin: 0;
  }

  .header-top {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .demo-btn {
    flex-shrink: 0;
    background: linear-gradient(135deg, #10b981 0%, #059669 100%);
    color: white;
    border: none;
    padding: 10px 18px;
    border-radius: 8px;
    font-size: 0.9rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    white-space: nowrap;
    height: fit-content;
    margin-left: auto;
  }

  .demo-btn:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(16, 185, 129, 0.3);
  }

  .demo-btn:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  form {
    display: flex;
    flex-direction: column;
    gap: 18px;
    margin-bottom: 24px;
  }

  .input-group {
    display: flex;
    flex-direction: column;
    gap: 8px;
  }

  label {
    font-size: 0.9rem;
    font-weight: 600;
    color: #1a1a2e;
  }

  input {
    padding: 12px 14px;
    border: 2px solid #e5e7eb;
    border-radius: 10px;
    font-size: 1rem;
    transition: all 0.3s ease;
    font-family: inherit;
    background: #f9fafb;
  }

  input:focus {
    outline: none;
    border-color: #667eea;
    background: white;
    box-shadow: 0 0 0 4px rgba(102, 126, 234, 0.1);
  }

  input::placeholder {
    color: #d1d5db;
  }

  input:disabled {
    background: #f3f4f6;
    cursor: not-allowed;
    color: #9ca3af;
  }

  .error-box {
    background: #fee2e2;
    color: #dc2626;
    padding: 12px 14px;
    border-radius: 10px;
    font-size: 0.9rem;
    border-left: 4px solid #dc2626;
  }

  .submit-btn {
    padding: 13px 20px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    border-radius: 10px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    text-transform: uppercase;
    letter-spacing: 0.5px;
    margin-top: 8px;
  }

  .submit-btn:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 12px 24px rgba(102, 126, 234, 0.4);
  }

  .submit-btn:disabled {
    opacity: 0.6;
    cursor: not-allowed;
  }

  .divider {
    text-align: center;
    color: #d1d5db;
    margin: 20px 0;
    font-size: 0.85rem;
    font-weight: 500;
    position: relative;
  }

  .divider::before,
  .divider::after {
    content: '';
    position: absolute;
    top: 50%;
    width: 45%;
    height: 1px;
    background: #e5e7eb;
  }

  .divider::before {
    left: 0;
  }

  .divider::after {
    right: 0;
  }

  .toggle-section {
    text-align: center;
  }

  .toggle-section p {
    font-size: 0.9rem;
    color: #888;
    margin: 0 0 12px 0;
  }

  .toggle-btn {
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border: none;
    padding: 10px 20px;
    border-radius: 10px;
    cursor: pointer;
    font-weight: 600;
    font-size: 0.9rem;
    transition: all 0.3s ease;
  }

  .toggle-btn:hover:not(:disabled) {
    transform: translateY(-2px);
    box-shadow: 0 8px 16px rgba(102, 126, 234, 0.3);
  }

  .toggle-btn:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }

  /* Tablet: 1 column layout */
  @media (max-width: 1024px) {
    .main-grid {
      grid-template-columns: 1fr;
      gap: 40px;
    }

    .features-section h2 {
      font-size: 2.2rem;
    }

    .features-grid {
      grid-template-columns: repeat(2, 1fr);
    }

    .form-card {
      max-width: 400px;
    }
  }

  /* Mobile */
  @media (max-width: 768px) {
    .content-wrapper {
      padding: 24px 16px;
    }

    .header {
      margin-bottom: 40px;
    }

    .logo {
      height: 50px;
    }

    .brand-text h1 {
      font-size: 1.8rem;
    }

    .main-grid {
      gap: 30px;
    }

    .features-section h2 {
      font-size: 1.8rem;
      margin-bottom: 12px;
    }

    .subtitle {
      font-size: 1rem;
      margin-bottom: 30px;
    }

    .features-grid {
      grid-template-columns: 1fr;
      gap: 16px;
    }

    .feature-card {
      padding: 16px;
    }

    .feature-icon {
      font-size: 1.8rem;
    }

    .feature-card h3 {
      font-size: 0.95rem;
    }

    .feature-card p {
      font-size: 0.85rem;
    }

    .form-card {
      padding: 28px 20px;
      max-width: 100%;
    }

    .form-header h2 {
      font-size: 1.5rem;
    }

    .form-header p {
      font-size: 0.9rem;
    }

    .header-top {
      flex-direction: column;
      gap: 8px;
    }

    .demo-btn {
      width: 100%;
      margin-top: 0;
    }
  }

  @media (max-width: 480px) {
    .content-wrapper {
      padding: 20px 14px;
    }

    .header {
      margin-bottom: 30px;
    }

    .logo-brand {
      gap: 12px;
    }

    .logo {
      height: 44px;
    }

    .brand-text h1 {
      font-size: 1.5rem;
    }

    .brand-text p {
      font-size: 0.8rem;
    }

    .main-grid {
      gap: 24px;
    }

    .features-section h2 {
      font-size: 1.6rem;
      margin-bottom: 8px;
    }

    .subtitle {
      font-size: 0.95rem;
      margin-bottom: 24px;
    }

    .features-grid {
      gap: 12px;
    }

    .feature-card {
      padding: 14px;
      border-radius: 10px;
    }

    .feature-icon {
      font-size: 1.6rem;
      margin-bottom: 8px;
    }

    .feature-card h3 {
      font-size: 0.9rem;
      margin-bottom: 6px;
    }

    .feature-card p {
      font-size: 0.8rem;
    }

    .form-card {
      padding: 24px 16px;
    }

    .form-header h2 {
      font-size: 1.3rem;
      margin-bottom: 8px;
    }

    .form-header p {
      font-size: 0.85rem;
    }

    .demo-btn {
      padding: 7px 14px;
      font-size: 0.8rem;
    }

    form {
      gap: 14px;
      margin-bottom: 16px;
    }

    label {
      font-size: 0.85rem;
    }

    input {
      padding: 11px 12px;
      font-size: 0.95rem;
    }

    .submit-btn {
      padding: 11px;
      font-size: 0.9rem;
      margin-top: 6px;
    }

    .toggle-btn {
      padding: 9px 18px;
      font-size: 0.85rem;
    }
  }
</style>
