# PennyWise account access prototype

A responsive sign-in and sign-up prototype built in SvelteKit from the supplied PennyWise reference screen.

## Run locally

1. Open this folder in Visual Studio Code.
2. Run `npm install` in the integrated terminal.
3. Run `npm run dev` and open the local URL printed in the terminal.

## Included flows

- Sign in with email and password fields, validation, password visibility, and a keep-signed-in checkbox.
- Create an account with name, email, password, and confirmation fields.
- Open a password-reset screen and receive clear prototype-only feedback.
- Switch between sign-in, sign-up, and reset screens without reloading.

## Accessibility

- Semantic landmarks, a skip link, persistent labels, and autocomplete hints.
- Keyboard-visible focus states, keyboard-operable controls, and reduced-motion support.
- Inline errors linked to inputs with `aria-invalid`, plus live feedback for form submission.
- Responsive layout and contrast-conscious colors.

## Prototype limitation

This is a front-end demonstration. It does not create accounts, save passwords, send reset email, or connect to an authentication service. Form submissions display honest demo feedback so no information is mistaken for being securely sent.

