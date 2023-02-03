<script lang="ts">
	import { invalidateAll } from '$app/navigation';

	async function handleLogout() {
		const response = await fetch('/api/auth/logout', {
			method: 'POST',
			headers: {
				accept: 'application/json'
			}
		});
		if (response.ok) {
			invalidateAll();
		}
	}
</script>

<form action="/api/auth/logout" method="POST" on:submit|preventDefault={handleLogout}>
	<button>Logout</button>
</form>
