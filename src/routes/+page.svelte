<script lang="ts">
	import { onMount } from 'svelte';
	import gapi from 'gapi-script';

	let isSignedIn = false;

	const CLIENT_ID = import.meta.env.VITE_GOOGLE_CLIENT_ID;
	const API_KEY = import.meta.env.VITE_GOOGLE_API_KEY;
	const SCOPES = 'https://www.googleapis.com/auth/fitness.activity.read';

	onMount(() => {
		function initClient() {
			gapi.client
				.init({
					apiKey: API_KEY,
					clientId: CLIENT_ID,
					discoveryDocs: ['https://www.googleapis.com/discovery/v1/apis/fitness/v1/rest'],
					scope: SCOPES
				})
				.then(() => {
					const authInstance = gapi.auth2.getAuthInstance();
					authInstance.isSignedIn.listen(updateSigninStatus);
					updateSigninStatus(authInstance.isSignedIn.get());
				});
		}

		gapi.load('client:auth2', initClient);
	});

	function updateSigninStatus(signedIn: boolean) {
		isSignedIn = signedIn;
	}

	function handleSignIn() {
		gapi.auth2.getAuthInstance().signIn();
	}

	function handleSignOut() {
		gapi.auth2.getAuthInstance().signOut();
	}
</script>

<main>
	{#if !isSignedIn}
		<button on:click={handleSignIn}>Conectar Google Fit</button>
	{:else}
		<p>✅ Conectado!</p>
		<button on:click={handleSignOut}>Sair</button>
	{/if}
</main>
