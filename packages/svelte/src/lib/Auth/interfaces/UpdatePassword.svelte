<script lang="ts">
	import type { SupabaseClient } from '@supabase/supabase-js';
	import Anchor from '$lib/UI/Anchor.svelte';
	import Button from '$lib/UI/Button.svelte';
	import Container from '$lib/UI/Container.svelte';
	import Input from '$lib/UI/Input.svelte';
	import Label from '$lib/UI/Label.svelte';
	import Message from '$lib/UI/Message.svelte';
	import { VIEWS, type I18nVariables, type ViewType } from '@supabase/auth-ui-shared';
	import type { Appearance } from '$lib/types';

	export let i18n: I18nVariables;
	export let supabaseClient: SupabaseClient;
	export let authView: ViewType;
	export let appearance: Appearance;
	export let showLinks = false;

	let password = '';
	let message = '';
	let error = '';
	let loading = false;

	async function handleSubmit() {
		loading = true;
		error = '';
		message = '';
		const { data, error: resetPasswordError } = await supabaseClient.auth.updateUser({
			password
		});
		if (resetPasswordError) error = resetPasswordError.message;
		else message = i18n.update_password?.confirmation_text as string;
		loading = false;
	}
</script>

<form id="auth-update-password" method="post" on:submit|preventDefault={handleSubmit}>
	<Container direction="vertical" gap="large" {appearance}>
		<Container direction="vertical" gap="large" {appearance}>
			<div>
				<Label for="password" {appearance}>
					{i18n?.update_password?.password_label}
				</Label>
				<Input
					id="password"
					type="password"
					name="password"
					autofocus
					placeholder={i18n?.update_password?.password_input_placeholder}
					bind:value={password}
					autocomplete="password"
					{appearance}
				/>
			</div>
			<Button type="submit" color="primary" {loading} {appearance}>
				{loading
					? i18n?.update_password?.loading_button_label
					: i18n?.update_password?.button_label}
			</Button>
		</Container>

		{#if showLinks}
			<Anchor
				on:click={(e) => {
					e.preventDefault();
					authView = VIEWS.SIGN_IN;
				}}
				href="#auth-magic-link"
				{appearance}>{i18n?.sign_in?.link_text}</Anchor
			>
		{/if}
		{#if message}
			<Message {appearance}>
				{message}
			</Message>
		{/if}
		{#if error}
			<Message color="danger" {appearance}>
				{error}
			</Message>
		{/if}
	</Container>
</form>

<style>
	form {
		width: 100%;
	}
</style>
