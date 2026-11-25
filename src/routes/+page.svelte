<script lang="ts">
	type Mode = 'login' | 'signup' | 'reset';
	type FieldErrors = Record<string, string>;

	let mode = $state<Mode>('login');
	let fullName = $state('');
	let email = $state('');
	let password = $state('');
	let confirmPassword = $state('');
	let remember = $state(true);
	let showPassword = $state(false);
	let errors = $state<FieldErrors>({});
	let message = $state('');
	let messageKind = $state<'success' | 'error'>('success');

	function clearFeedback() {
		errors = {};
		message = '';
	}

	function setMode(next: Mode) {
		mode = next;
		clearFeedback();
		showPassword = false;
		requestAnimationFrame(() => document.querySelector('#form-title')?.focus());
	}

	function submitForm(event: SubmitEvent) {
		event.preventDefault();
		clearFeedback();
		const nextErrors: FieldErrors = {};
		const trimmedEmail = email.trim();
		if (!trimmedEmail) nextErrors.email = 'Enter your email address.';
		else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(trimmedEmail)) nextErrors.email = 'Enter a valid email address.';

		if (mode !== 'reset') {
			if (!password) nextErrors.password = 'Enter your password.';
			else if (mode === 'signup' && password.length < 8) nextErrors.password = 'Use at least 8 characters.';
		}
		if (mode === 'signup' && !fullName.trim()) nextErrors.name = 'Enter your name.';
		if (mode === 'signup' && confirmPassword !== password) nextErrors.confirmPassword = 'Your passwords do not match.';

		if (Object.keys(nextErrors).length) {
			errors = nextErrors;
			messageKind = 'error';
			message = 'A few details need your attention. Check the fields below.';
			const first = Object.keys(nextErrors)[0];
			const inputIds: Record<string, string> = { name: 'full-name', email: 'email', password: 'password', confirmPassword: 'confirm-password' };
			requestAnimationFrame(() => document.getElementById(inputIds[first])?.focus());
			return;
		}

		messageKind = 'success';
		if (mode === 'reset') message = 'Password reset is ready to connect. This prototype does not send email yet.';
		else if (mode === 'signup') message = `Thanks, ${fullName.trim()}! Your sign-up flow is working. Connect an authentication service to create a real account.`;
		else message = 'Your sign-in details look good. Connect an authentication service to securely sign in.';
	}
</script>

<svelte:head>
	<title>{mode === 'signup' ? 'Create account' : mode === 'reset' ? 'Reset password' : 'Sign in'} Â· PennyWise</title>
	<meta name="description" content="Sign in or create a PennyWise account to manage your money in Naira." />
	<link rel="preconnect" href="https://fonts.googleapis.com" />
	<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin="anonymous" />
	<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@400;500;600;700&family=Manrope:wght@400;500;600;700;800&display=swap" rel="stylesheet" />
</svelte:head>

<main class="page-shell">
	<a class="skip-link" href="#auth-card">Skip to account access</a>
	<header class="site-header" aria-label="PennyWise">
		<a class="brand" href="/" aria-label="PennyWise home">
			<span class="brand-mark" aria-hidden="true"><svg viewBox="0 0 40 40" fill="none"><path d="M20 3.5 34 9v10.6c0 8.4-5.8 14.6-14 17-8.2-2.4-14-8.6-14-17V9l14-5.5Z" fill="currentColor"/><path d="M13.2 14h7.2a5.5 5.5 0 1 1 0 11h-2.8v4h-4.4V14Zm4.4 3.8v3.4h2.6a1.7 1.7 0 1 0 0-3.4h-2.6Z" fill="#fff"/><path d="M23.7 14h4.4v15h-4.4z" fill="#fff"/></svg></span>
			<span class="brand-name">Penny<span>Wise</span></span>
		</a>
		<p class="header-note">Your money, moving wisely.</p>
	</header>

	<section class="auth-layout" aria-label="Account access">
		<aside class="welcome-panel" aria-label="PennyWise budgeting tools">
			<div class="orb orb-one"></div><div class="orb orb-two"></div>
			<div class="welcome-content">
				<span class="eyebrow"><span class="eyebrow-dot"></span>A clearer view of your money</span>
				<h1>Make every<br />naira <em>count.</em></h1>
				<p>Simple tools to help you stay on top of your spending and feel good about what comes next.</p>
				<div class="money-card">
					<span class="money-label">Your money, in focus</span>
					<div class="money-amount"><span>â‚¦</span> 248,500<span class="amount-decimal">.00</span></div>
					<div class="money-foot"><span class="trend-icon" aria-hidden="true">â†—</span>Spending with intention</div>
					<div class="chart" aria-hidden="true">{#each Array(12) as _, i}<i class:bar-tall={i % 4 === 0} class:bar-mid={i % 3 === 0}></i>{/each}</div>
				</div>
				<div class="welcome-foot"><span class="sparkle" aria-hidden="true">âœ¦</span>A little more clarity, every day.</div>
			</div>
		</aside>

		<section class="form-panel" id="auth-card" aria-labelledby="form-title">
			<div class="form-inner">
				<a class="brand mobile-brand" href="/" aria-label="PennyWise home">
					<span class="brand-mark" aria-hidden="true"><svg viewBox="0 0 40 40" fill="none"><path d="M20 3.5 34 9v10.6c0 8.4-5.8 14.6-14 17-8.2-2.4-14-8.6-14-17V9l14-5.5Z" fill="currentColor"/><path d="M13.2 14h7.2a5.5 5.5 0 1 1 0 11h-2.8v4h-4.4V14Zm4.4 3.8v3.4h2.6a1.7 1.7 0 1 0 0-3.4h-2.6Z" fill="#fff"/><path d="M23.7 14h4.4v15h-4.4z" fill="#fff"/></svg></span>
					<span class="brand-name">Penny<span>Wise</span></span>
				</a>
				<div class="form-heading">
					<span class="welcome-back">{mode === 'signup' ? 'GET STARTED' : mode === 'reset' ? 'PASSWORD HELP' : 'WELCOME BACK'}</span>
					<h2 id="form-title" tabindex="-1">{mode === 'signup' ? 'Create your account' : mode === 'reset' ? 'Reset your password' : 'Sign in to your account'}</h2>
					<p>{mode === 'signup' ? 'Start making every naira count.' : mode === 'reset' ? 'Enter your email and weâ€™ll show you the next step.' : 'Pick up where you left off with your money.'}</p>
				</div>

				{#if message}
					<div class="form-message" class:error={messageKind === 'error'} role="status" aria-live="polite">{message}</div>
				{/if}

				<form onsubmit={submitForm} novalidate>
					{#if mode === 'signup'}
						<div class="field">
							<label for="full-name">Full name</label>
							<div class="input-wrap" class:invalid={!!errors.name}><span class="input-icon" aria-hidden="true"><svg viewBox="0 0 20 20" fill="none"><circle cx="10" cy="6" r="3"/><path d="M4 17c.4-3 2.4-4.5 6-4.5s5.6 1.5 6 4.5"/></svg></span><input id="full-name" name="name" type="text" autocomplete="name" placeholder="e.g. Tunde Adebayo" bind:value={fullName} aria-invalid={errors.name ? 'true' : undefined} aria-describedby={errors.name ? 'name-error' : undefined} /></div>
							{#if errors.name}<p class="field-error" id="name-error">{errors.name}</p>{/if}
						</div>
					{/if}

					<div class="field">
						<label for="email">Email address</label>
						<div class="input-wrap" class:invalid={!!errors.email}><span class="input-icon" aria-hidden="true"><svg viewBox="0 0 20 20" fill="none"><rect x="2.5" y="4" width="15" height="12" rx="2.5"/><path d="m3.5 5.5 6.5 5 6.5-5"/></svg></span><input id="email" name="email" type="email" autocomplete="email" placeholder="you@example.com" inputmode="email" required bind:value={email} aria-invalid={errors.email ? 'true' : undefined} aria-describedby={errors.email ? 'email-error' : undefined} /></div>
						{#if errors.email}<p class="field-error" id="email-error">{errors.email}</p>{/if}
					</div>

					{#if mode !== 'reset'}
						<div class="field">
							<div class="label-row"><label for="password">Password</label>{#if mode === 'login'}<button class="text-button" type="button" onclick={() => setMode('reset')}>Forgot password?</button>{/if}</div>
							<div class="input-wrap" class:invalid={!!errors.password}><span class="input-icon" aria-hidden="true"><svg viewBox="0 0 20 20" fill="none"><rect x="3.5" y="8" width="13" height="9" rx="2"/><path d="M6.5 8V6a3.5 3.5 0 1 1 7 0v2M10 11.5v2"/></svg></span><input id="password" name="password" type={showPassword ? 'text' : 'password'} autocomplete={mode === 'signup' ? 'new-password' : 'current-password'} placeholder="{mode === 'signup' ? 'At least 8 characters' : 'Enter your password'}" required bind:value={password} aria-invalid={errors.password ? 'true' : undefined} aria-describedby={errors.password ? 'password-error' : mode === 'signup' ? 'password-hint' : undefined} /><button class="visibility-toggle" type="button" aria-label={showPassword ? 'Hide password' : 'Show password'} aria-pressed={showPassword} onclick={() => (showPassword = !showPassword)}><svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M2 10s2.9-5 8-5 8 5 8 5-2.9 5-8 5-8-5-8-5Z"/><circle cx="10" cy="10" r="2.2"/></svg></button></div>
							{#if mode === 'signup'}<p class="field-hint" id="password-hint">Use at least 8 characters.</p>{/if}
							{#if errors.password}<p class="field-error" id="password-error">{errors.password}</p>{/if}
						</div>
					{/if}

					{#if mode === 'signup'}
						<div class="field">
							<label for="confirm-password">Confirm password</label>
							<div class="input-wrap" class:invalid={!!errors.confirmPassword}><span class="input-icon" aria-hidden="true"><svg viewBox="0 0 20 20" fill="none"><rect x="3.5" y="8" width="13" height="9" rx="2"/><path d="M6.5 8V6a3.5 3.5 0 1 1 7 0v2M8 12l1.5 1.5L12.5 11"/></svg></span><input id="confirm-password" name="confirmPassword" type="password" autocomplete="new-password" placeholder="Re-enter your password" required bind:value={confirmPassword} aria-invalid={errors.confirmPassword ? 'true' : undefined} aria-describedby={errors.confirmPassword ? 'confirm-error' : undefined} /></div>
							{#if errors.confirmPassword}<p class="field-error" id="confirm-error">{errors.confirmPassword}</p>{/if}
						</div>
					{/if}

					{#if mode === 'login'}
						<label class="check-row" for="remember"><input id="remember" name="remember" type="checkbox" bind:checked={remember} /><span class="custom-check" aria-hidden="true"><svg viewBox="0 0 12 10"><path d="m1 5 3.2 3L11 1" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg></span><span>Keep me signed in</span></label>
					{/if}

					<button class="submit-button" type="submit"><span>{mode === 'signup' ? 'Create account' : mode === 'reset' ? 'Continue' : 'Sign in'}</span><svg viewBox="0 0 20 20" fill="none" aria-hidden="true"><path d="M3.5 10h12m-5-5 5 5-5 5"/></svg></button>
				</form>

				<div class="form-divider"><span>SECURE &amp; PRIVATE</span></div>
				<p class="switch-copy">
					{#if mode === 'signup'}Already have an account? <button class="text-button switch-button" type="button" onclick={() => setMode('login')}>Sign in</button>
					{:else if mode === 'reset'}<button class="text-button switch-button" type="button" onclick={() => setMode('login')}>Back to sign in</button>
					{:else}New to PennyWise? <button class="text-button switch-button" type="button" onclick={() => setMode('signup')}>Create an account</button>{/if}
				</p>
				<p class="legal-copy">Prototype only. Your details are not sent or saved.</p>
			</div>
		</section>
	</section>

	<footer class="page-footer"><span>Â© {new Date().getFullYear()} PennyWise</span><span class="footer-dot" aria-hidden="true"></span><span>Built for a brighter financial future</span></footer>
</main>

<style>
	:global(*) { box-sizing: border-box; }
	:global(html) { color-scheme: light; }
	:global(body) { min-width: 320px; min-height: 100vh; margin: 0; color: #15271f; background: #f4f7f3; font-family: 'DM Sans', sans-serif; font-synthesis: none; text-rendering: optimizeLegibility; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; }
	:global(button), :global(input) { font: inherit; }
	:global(button), :global(a) { -webkit-tap-highlight-color: transparent; }
	.page-shell { min-height: 100vh; display: flex; flex-direction: column; padding: 0 clamp(22px, 6vw, 96px); }
	.skip-link { position: fixed; z-index: 20; top: 12px; left: 12px; padding: 12px 16px; color: #fff; background: #143a2a; border-radius: 8px; transform: translateY(-160%); }
	.skip-link:focus { transform: translateY(0); }
	.site-header { height: 92px; display: flex; align-items: center; justify-content: space-between; }
	.brand { display: inline-flex; align-items: center; gap: 10px; text-decoration: none; }
	.brand-mark { width: 36px; height: 36px; display: grid; place-items: center; color: #078653; }
	.brand-mark svg { width: 100%; height: 100%; }
	.brand-name { color: #1a2922; font: 800 20px Manrope, sans-serif; letter-spacing: -1px; }
	.brand-name span { color: #078653; }
	.header-note { margin: 0; color: #7b8981; font-size: 13px; }
	.auth-layout { flex: 1; width: min(1100px, 100%); min-height: 650px; margin: 5px auto 34px; display: grid; grid-template-columns: .93fr 1.07fr; overflow: hidden; background: #fff; border: 1px solid #e7ece8; border-radius: 24px; box-shadow: 0 22px 65px rgba(26, 53, 38, .09), 0 3px 10px rgba(26, 53, 38, .03); }
	.welcome-panel { position: relative; overflow: hidden; display: flex; align-items: center; padding: 58px clamp(34px, 5vw, 68px); color: #fff; background: #0d4834; }
	.welcome-panel::before { position: absolute; inset: 0; content: ''; background: radial-gradient(ellipse at 18% 82%, rgba(54,179,125,.18), transparent 44%), linear-gradient(155deg, rgba(255,255,255,.025), transparent 60%); }
	.orb { position: absolute; border: 1px solid rgba(211,255,231,.1); border-radius: 50%; }
	.orb-one { width: 420px; height: 420px; right: -275px; top: -214px; box-shadow: 0 0 0 38px rgba(211,255,231,.025), 0 0 0 78px rgba(211,255,231,.02); }
	.orb-two { width: 290px; height: 290px; left: -208px; bottom: -207px; box-shadow: 0 0 0 34px rgba(211,255,231,.035), 0 0 0 72px rgba(211,255,231,.02); }
	.welcome-content { position: relative; z-index: 1; width: 100%; max-width: 420px; margin: 0 auto; }
	.eyebrow { display: inline-flex; align-items: center; gap: 9px; color: #c4e1d2; font-size: 11px; font-weight: 700; letter-spacing: 1.1px; text-transform: uppercase; }
	.eyebrow-dot { width: 7px; height: 7px; border-radius: 50%; background: #6bd69c; box-shadow: 0 0 0 4px rgba(107,214,156,.12); }
	h1 { margin: 22px 0 13px; font: 600 clamp(38px, 4vw, 53px)/1.1 Manrope, sans-serif; letter-spacing: -2.5px; }
	h1 em { color: #73d49f; font-style: normal; }
	.welcome-content > p { max-width: 340px; margin: 0; color: #c1d8cc; font-size: 14px; line-height: 1.75; }
	.money-card { position: relative; margin-top: 39px; padding: 19px 21px 17px; overflow: hidden; border: 1px solid rgba(255,255,255,.14); border-radius: 14px; background: rgba(255,255,255,.085); backdrop-filter: blur(10px); }
	.money-label { display: block; color: #bbd5c8; font-size: 11px; }
	.money-amount { position: relative; z-index: 1; margin-top: 7px; font: 700 27px Manrope, sans-serif; letter-spacing: -.8px; }
	.money-amount > span:first-child { color: #8fdfb2; font-size: 19px; }
	.money-amount .amount-decimal { color: #cee5d8; font-size: 17px; }
	.money-foot { position: relative; z-index: 1; margin-top: 8px; color: #c5dfd1; font-size: 11px; }
	.trend-icon { margin-right: 4px; color: #74d69e; font-size: 15px; }
	.chart { position: absolute; right: 18px; bottom: 17px; width: 117px; height: 39px; display: flex; align-items: end; gap: 5px; opacity: .58; }
	.chart i { flex: 1; height: 35%; border-radius: 3px 3px 0 0; background: #63ce93; }
	.chart i.bar-mid { height: 63%; }.chart i.bar-tall { height: 100%; }
	.welcome-foot { display: flex; align-items: center; gap: 9px; margin-top: 24px; color: #c2dfcf; font-size: 12px; }
	.sparkle { color: #86e2ae; font-size: 16px; }
	.form-panel { display: flex; align-items: center; justify-content: center; padding: 56px clamp(30px, 5.6vw, 78px); }
	.form-inner { width: 100%; max-width: 390px; }
	.mobile-brand { display: none; }
	.form-heading { margin-bottom: 29px; }
	.welcome-back { color: #078653; font-size: 10px; font-weight: 700; letter-spacing: 1.5px; }
	.form-heading h2 { margin: 9px 0 7px; color: #15271f; font: 700 clamp(24px, 2.5vw, 29px)/1.25 Manrope, sans-serif; letter-spacing: -.8px; }
	.form-heading p { margin: 0; color: #718078; font-size: 13px; line-height: 1.6; }
	.form-message { margin: -10px 0 20px; padding: 11px 13px; border: 1px solid #bfe6ce; border-radius: 8px; color: #17623f; background: #f0faf3; font-size: 12px; line-height: 1.5; }
	.form-message.error { border-color: #f0c7c9; color: #922f37; background: #fff5f5; }
	.field { margin-bottom: 19px; }
	.field label, .label-row label { display: block; margin-bottom: 8px; color: #27382f; font-size: 12px; font-weight: 600; }
	.label-row { display: flex; align-items: center; justify-content: space-between; gap: 12px; }
	.label-row label { margin-bottom: 8px; }
	.input-wrap { min-height: 48px; position: relative; display: flex; align-items: center; border: 1px solid #dfe7e1; border-radius: 9px; background: #fff; transition: border-color .18s, box-shadow .18s; }
	.input-wrap:hover { border-color: #c4d4c9; }.input-wrap:focus-within { border-color: #078653; box-shadow: 0 0 0 3px rgba(8,134,83,.12); }.input-wrap.invalid { border-color: #bd3c45; }
	.input-icon { flex: 0 0 45px; display: flex; justify-content: center; color: #93a198; }
	.input-icon svg { width: 17px; height: 17px; stroke: currentColor; stroke-width: 1.45; stroke-linecap: round; stroke-linejoin: round; }
	.input-wrap input { width: 100%; min-width: 0; height: 46px; padding: 0 13px 0 0; border: 0; outline: 0; color: #1d3026; background: transparent; font-size: 13px; }
	.input-wrap input::placeholder { color: #a1aca5; opacity: 1; }
	.visibility-toggle { width: 43px; height: 42px; flex: 0 0 43px; display: grid; place-items: center; margin-right: 2px; border: 0; border-radius: 7px; color: #829188; background: transparent; cursor: pointer; }
	.visibility-toggle:hover { color: #078653; background: #f3f8f4; }.visibility-toggle svg { width: 19px; height: 19px; stroke: currentColor; stroke-width: 1.5; stroke-linecap: round; stroke-linejoin: round; }
	.text-button { padding: 0; border: 0; color: #078653; background: transparent; font-size: 11px; font-weight: 600; cursor: pointer; }.text-button:hover { color: #066b44; text-decoration: underline; text-underline-offset: 3px; }
	:global(a:focus-visible), button:focus-visible, .form-heading h2:focus-visible { outline: 3px solid #4dbe8b; outline-offset: 3px; }
	.field-hint, .field-error { margin: 6px 0 0; font-size: 11px; line-height: 1.4; }.field-hint { color: #74837a; }.field-error { color: #bd3c45; }
	.check-row { position: relative; display: inline-flex; align-items: center; gap: 9px; margin: 0 0 22px; color: #526159; font-size: 11px; cursor: pointer; user-select: none; }
	.check-row input { position: absolute; width: 1px; height: 1px; opacity: 0; }.custom-check { width: 16px; height: 16px; display: grid; place-items: center; border: 1px solid #cbd6ce; border-radius: 4px; color: transparent; background: white; transition: all .15s; }.custom-check svg { width: 10px; height: 9px; }
	.check-row input:checked + .custom-check { border-color: #078653; color: white; background: #078653; }.check-row input:focus-visible + .custom-check { outline: 3px solid rgba(8,134,83,.24); outline-offset: 2px; }
	.submit-button { width: 100%; height: 49px; display: flex; align-items: center; justify-content: center; gap: 10px; border: 0; border-radius: 9px; color: #fff; background: #078653; box-shadow: 0 4px 9px rgba(8,134,83,.16); font-size: 13px; font-weight: 700; cursor: pointer; transition: background .18s, transform .18s, box-shadow .18s; }
	.submit-button:hover { background: #066b44; box-shadow: 0 6px 14px rgba(8,134,83,.22); transform: translateY(-1px); }.submit-button:active { transform: translateY(0); }.submit-button svg { width: 17px; height: 17px; stroke: currentColor; stroke-width: 1.8; stroke-linecap: round; stroke-linejoin: round; }
	.form-divider { position: relative; display: flex; align-items: center; justify-content: center; margin: 26px 0 18px; color: #a0aaa3; font-size: 9px; font-weight: 700; letter-spacing: 1.25px; }.form-divider::before { position: absolute; right: 0; left: 0; height: 1px; background: #edf0ed; content: ''; }.form-divider span { position: relative; padding: 0 12px; background: #fff; }
	.switch-copy { margin: 0; color: #718078; text-align: center; font-size: 12px; }.switch-button { margin-left: 3px; font-size: 12px; }.legal-copy { margin: 25px 0 0; color: #87958c; text-align: center; font-size: 10px; line-height: 1.7; }
	.page-footer { display: flex; justify-content: center; align-items: center; gap: 10px; padding: 0 0 25px; color: #929d95; font-size: 10px; }.footer-dot { width: 3px; height: 3px; border-radius: 50%; background: #b8c2bb; }
	@media (max-width: 820px) { .page-shell { padding: 0 28px; }.auth-layout { width: min(600px,100%); min-height: 0; grid-template-columns: 1fr; margin-top: 8px; }.welcome-panel { display: none; }.form-panel { padding: 48px clamp(28px, 8vw, 64px); }.mobile-brand { display: inline-flex; margin-bottom: 38px; } }
	@media (max-width: 520px) { :global(body) { background: #fff; }.page-shell { padding: 0 21px; }.site-header { height: 68px; }.site-header > .brand { display: none; }.header-note { margin-left: auto; font-size: 11px; }.auth-layout { width: 100%; margin: 0 auto 25px; border: 0; border-radius: 0; box-shadow: none; }.form-panel { padding: 34px 0 40px; }.mobile-brand { margin-bottom: 42px; }.form-heading { margin-bottom: 27px; }.form-heading h2 { font-size: 26px; }.page-footer { flex-wrap: wrap; padding-bottom: 18px; font-size: 9px; }.legal-copy { margin-top: 22px; } }
	@media (prefers-reduced-motion: reduce) { *, *::before, *::after { scroll-behavior: auto !important; transition-duration: .01ms !important; animation-duration: .01ms !important; animation-iteration-count: 1 !important; } }
</style>

