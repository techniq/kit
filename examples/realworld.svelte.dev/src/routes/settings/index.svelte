<script context="module">
	export function load({ session }) {
		if (!session.user) {
			return { redirect: '/login', status: 302 };
		}
	}
</script>

<script>
	import { goto } from '$app/navigation';
	import ListErrors from '$lib/ListErrors.svelte';
	import SettingsForm from './_SettingsForm.svelte';
	import { post } from '$lib/utils.js';

	let inProgress;
	let errors;

	const { session } = stores();

	async function logout() {
		await post(`auth/logout`);
		$session.user = null;
		goto('/');
	}

	async function save(event) {
		inProgress = true;

		const response = await post(`auth/save`, event.detail);

		errors = response.errors;
		if (response.user) $session.user = response.user;

		inProgress = false;
	}
</script>

<svelte:head>
	<title>Settings • Conduit</title>
</svelte:head>

<div class="settings-page">
	<div class="container page">
		<div class="row">
			<div class="col-md-6 offset-md-3 col-xs-12">

				<h1 class="text-xs-center">Your Settings</h1>

				<ListErrors {errors}/>

				<SettingsForm on:save={save} {...$session.user} {inProgress}/>

				<hr />

				<button class="btn btn-outline-danger" on:click={logout}>
					Or click here to logout.
				</button>
			</div>
		</div>
	</div>
</div>