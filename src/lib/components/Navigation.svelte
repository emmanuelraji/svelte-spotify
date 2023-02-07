<script lang="ts">
	import { tick, type ComponentType } from 'svelte';
	import { page } from '$app/stores';
	import { Home, Search, ListMusic, type Icon } from 'lucide-svelte';
	import logo from '$assets/Spotify_Logo_RGB_White.png';
	import { fade } from 'svelte/transition';
	import { beforeNavigate } from '$app/navigation';

	export let desktop: boolean;

	let isMobileMenuOpen = false;
	$: isOpen = desktop || isMobileMenuOpen;

	let openMenuButton: HTMLButtonElement;
	let closeMenuButton: HTMLButtonElement;
	let lastFocusableElement: HTMLAnchorElement;

	const menuItems: { path: string; label: string; icon: ComponentType<Icon> }[] = [
		{ path: '/', label: 'Home', icon: Home },
		{ path: '/search', label: 'Search', icon: Search },
		{ path: '/playlists', label: 'Playlists', icon: ListMusic }
	];

	async function openMenu() {
		isMobileMenuOpen = true;
		await tick();
		closeMenuButton.focus();
	}

	async function closeMenu() {
		isMobileMenuOpen = false;
		await tick();
		openMenuButton.focus();
	}

	function moveFocusToBottom(e: KeyboardEvent) {
		if (desktop) return;
		if (e.key === 'Tab' && e.shiftKey) {
			e.preventDefault();
			lastFocusableElement.focus();
		}
	}

	function moveFocusToTop(e: KeyboardEvent) {
		if (desktop) return;
		if (e.key === 'Tab' && !e.shiftKey) {
			e.preventDefault();
			closeMenuButton.focus();
		}
	}

	function handleEscape(e: KeyboardEvent) {
		if (e.key === 'Escape') {
			closeMenu();
		}
	}

	beforeNavigate(() => {
		isMobileMenuOpen = false;
	});
</script>

<svelte:head>
	{#if !desktop && isMobileMenuOpen}
		<style>
			body {
				overflow: hidden;
			}
		</style>
	{/if}
</svelte:head>

<div class="nav-content" class:desktop class:mobile={!desktop}>
	{#if !desktop && isMobileMenuOpen}
		<div
			class="overlay"
			on:keyup={handleEscape}
			on:click={closeMenu}
			transition:fade={{ duration: 200 }}
		/>
	{/if}
	<nav aria-label="Main">
		{#if !desktop}
			<button bind:this={openMenuButton} on:click={openMenu} aria-expanded={!isOpen}>Open</button>
		{/if}
		<div
			class="nav-content-inner"
			class:is-hidden={!isOpen}
			style:visibility={isOpen ? 'visible' : 'hidden'}
			on:keyup={handleEscape}
		>
			{#if !desktop}
				<button bind:this={closeMenuButton} on:click={closeMenu} on:keydown={moveFocusToBottom}>
					Close
				</button>
			{/if}
			<img src={logo} alt="Spotify" class="logo" />
			<ul>
				{#each menuItems as item, index}
					{@const iconProps = {
						focusable: 'false',
						'aria-hidden': true,
						color: 'var(--text-color)',
						size: 25,
						strokeWidth: 2
					}}
					<li class:active={item.path === $page.url.pathname}>
						{#if menuItems.length === index + 1}
							<a href={item.path} bind:this={lastFocusableElement} on:keydown={moveFocusToTop}>
								<svelte:component this={item.icon} {...iconProps} />
								{item.label}
							</a>
						{:else}
							<a href={item.path}>
								<svelte:component this={item.icon} {...iconProps} />
								{item.label}
							</a>
						{/if}
					</li>
				{/each}
			</ul>
		</div>
	</nav>
</div>

<style lang="scss">
	.nav-content {
		.overlay {
			position: fixed;
			top: 0;
			left: 0;
			width: 100%;
			height: 100%;
			background-color: var(--sidebar-color);
			opacity: 0.75;
			z-index: 100;
			@include breakpoint.up('md') {
				display: none;
			}
		}
		.logo {
			max-width: 100%;
			width: 130px;
		}
		.nav-content-inner {
			padding: 20px;
			min-width: var(--sidebar-width);
			background-color: var(--sidebar-color);
			height: 100vh;
			overflow: auto;
			display: none;
			ul {
				padding: 0;
				margin: 20px 0;
				list-style: none;
				li {
					&.active {
						a {
							opacity: 1;
						}
					}
					a {
						display: flex;
						align-items: center;
						text-decoration: none;
						color: var(--text-color);
						font-size: functions.toRem(14);
						font-weight: 500;
						padding: 5px;
						margin: 10px 0;
						opacity: 0.7;
						transition: opacity 0.2s;
						&:hover,
						&:focus {
							opacity: 1;
						}
						:global(svg) {
							margin-right: 12px;
						}
					}
				}
			}
		}
		&.desktop {
			position: sticky;
			top: 0;
			.nav-content-inner {
				@include breakpoint.up('md') {
					display: block;
				}
			}
		}
		&.mobile .nav-content-inner {
			position: fixed;
			top: 0;
			left: 0;
			z-index: 100;
			transition: transform 200ms, opacity 200ms;
			&.is-hidden {
				transition: transform 200ms, opacity 200ms, visibility 200ms;
				transform: translateX(-100%);
				opacity: 0;
			}
			@include breakpoint.down('md') {
				display: block;
			}
		}
	}
</style>
