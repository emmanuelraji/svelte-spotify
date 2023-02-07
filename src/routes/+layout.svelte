<script lang="ts">
	import type { LayoutData } from './$types';
	import { Navigation, Header } from '$components';
	import 'modern-normalize/modern-normalize.css';
	import '../styles/main.scss';

	export let data: LayoutData;

	$: user = data.user;

	let topbar: HTMLElement;
	let scrollY: number;
	let headerOpacity = 0;

	$: if (topbar) {
		headerOpacity = scrollY / topbar.offsetHeight < 1 ? scrollY / topbar.offsetHeight : 1;
	}
</script>

<svelte:window bind:scrollY />

<div id="main">
	{#if user}
		<div id="sidebar">
			<Navigation desktop={true} />
		</div>
	{/if}
	<div id="content">
		<div id="topbar" bind:this={topbar}>
			<Header />
			<div
				class="topbar-bg"
				style:background-color="var(--header-color)"
				style:opacity={headerOpacity}
			/>
		</div>
		<main id="main-content" class:logged-in={user}>
			<slot />
		</main>
	</div>
</div>

<style lang="scss">
	#main {
		display: flex;
		#content {
			flex: 1;
			#topbar {
				position: fixed;
				width: 100%;
				height: var(--header-height);
				z-index: 100;
				padding: 0 15px;
				display: flex;
				align-items: center;
				.topbar-bg {
					position: absolute;
					width: 100%;
					height: 100%;
					top: 0;
					left: 0;
					z-index: -1;
				}
				@include breakpoint.up('md') {
					padding: 0 30px;
					width: calc(100% - var(--sidebar-width));
				}
			}
			main#main-content {
				padding: 30px 15px 60px;
				@include breakpoint.up('md') {
					padding: 30px 30px 60px;
				}
				&.logged-in {
					padding-top: calc(30px + var(--header-height));
				}
			}
		}
	}
</style>
