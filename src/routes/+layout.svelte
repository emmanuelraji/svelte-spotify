<script lang="ts">
	import type { LayoutData } from './$types';
	import { LogoutButton } from '$components';
	import 'modern-normalize/modern-normalize.css';
	import '../styles/main.scss';

	export let data: LayoutData;

	$: user = data.user;
</script>

{#if user}
	<p>Hello, {user.display_name}</p>
	<LogoutButton />
{/if}

<slot />

<!-- <style lang="scss">
</style> -->
