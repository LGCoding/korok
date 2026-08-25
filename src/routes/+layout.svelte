<script lang="ts">
	import './layout.css';
	import 'leaflet/dist/leaflet.css';
	import favicon from '#lib/assets/korok_hunt_logo.png';

	import { ModeWatcher } from 'mode-watcher';
	import type { PageServerData } from './$types';
	import Button from '#lib/components/ui/button/button.svelte';
	import { enhance } from '$app/forms';
	import { cn } from '$lib/utils';
	import { page } from '$app/state';

	let { children, data }: PageServerData = $props();
</script>

{#snippet link({ label, href }: { label: string; href: string })}
	<li>
		<a
			class={cn('block border-b-2 border-transparent px-0 py-3 hover:border-accent lg:p-4', {
				'border-accent': page.url.pathname === href
			})}
			{href}>{label}</a
		>
	</li>
{/snippet}

<ModeWatcher />
<svelte:head><link rel="icon" href={favicon} /> <title>Korok Hunt</title></svelte:head>
<header class="flex flex-wrap items-center border-b px-6 py-2 lg:px-16 lg:py-0">
	<div class="flex flex-1 items-center justify-between">
		<a href="/">
			<img class="h-12 w-12" alt="logo" src="korok_hunt_logo.png" />
		</a>
	</div>

	<label for="menu-toggle" class="pointer-cursor block lg:hidden">
		<svg
			class="fill-current text-foreground"
			xmlns="http://www.w3.org/2000/svg"
			width="20"
			height="20"
			viewBox="0 0 20 20"
			><title>menu</title><path d="M0 3h20v2H0V3zm0 6h20v2H0V9zm0 6h20v2H0v-2z"></path>
		</svg>
	</label>
	<input class="hidden" type="checkbox" id="menu-toggle" />

	<div class="hidden w-full lg:flex lg:w-auto lg:items-center" id="menu">
		<nav>
			<ul class="items-center justify-between pt-4 text-base text-foreground lg:flex lg:pt-0">
				{@render link({ href: '/', label: 'Home' })}
				{@render link({ href: '/leaderboard', label: 'Leaderboard' })}
				{@render link({ href: '/korok-stats', label: 'All Koroks' })}
				{#if data.user?.role === 'admin'}
					{@render link({ href: '/admin', label: 'Admin' })}
				{/if}
				{#if !data.user}
					{@render link({ href: '/login', label: 'Login/Register' })}
				{:else}
					<form
						class="flex flex-1 items-center justify-between"
						method="post"
						action="/login/?/signOut"
						use:enhance
					>
						<Button variant="outline" type="submit">Sign out</Button>
					</form>
				{/if}
			</ul>
		</nav>
		<!-- <a href="#" class="pointer-cursor mb-4 flex items-center justify-start lg:mb-0 lg:ml-4">
			<img
				class="h-10 w-10 rounded-full border-2 border-transparent hover:border-indigo-400"
				src="https://pbs.twimg.com/profile_images/1128143121475342337/e8tkhRaz_normal.jpg"
				alt="Andy Leverenz"
			/>
		</a> -->
	</div>
</header>
{@render children()}

<style>
	#menu-toggle:checked + #menu {
		display: block;
	}
</style>
