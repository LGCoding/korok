<script lang="ts">
	import * as Card from '$lib/components/ui/card';
	import { onMount } from 'svelte';
	import { logFind } from '../query/korok.remote';
	import type { PageData } from './$types';
	import { CircleUserRound } from 'lucide-svelte';

	let { data }: { data: PageData } = $props();
	let failed = $state(false);
	let found = $state(false);
	let yourFinds = $state(0);
	let korokFinds = $state(0);
	let korok: {
		number: number;
		id: string;
		type: number;
		description: string;
		lat: number;
		lng: number;
		release: number;
		isRelease: boolean;
	} | null = $state(null);
	onMount(async () => {
		if (!data.user || !data.id) return;
		let e = await logFind({ korokId: data.id, userId: data.user.id, time: new Date() });
		if (e) {
			korok = e.korok;
			found = e.found;
			yourFinds = e.userFinds ?? 0;
			korokFinds = e.korokFinds ?? 0;
		} else {
			failed = true;
		}
	});
</script>

<div class="m-8">
	{#if !data.user}
		<Card.Root>
			<Card.Header>
				<Card.Title class="flex flex-col items-center text-3xl">
					<CircleUserRound size="20rem" strokeWidth={1} />
					<p>You need to login or register to find a korok. Go to the login/register page.</p>
				</Card.Title>
			</Card.Header>
		</Card.Root>
	{:else if failed}
		<Card.Root>
			<Card.Header>
				<Card.Title class="flex flex-col items-center text-3xl">
					<img class="h-80" src="/unknown.svg" alt="unknown" />
					<p>Failed to find korok. Please try again.</p>
				</Card.Title>
				<Card.Action class="text-3xl">#??</Card.Action>
			</Card.Header>
		</Card.Root>
	{/if}

	{#if korok}
		<Card.Root>
			<Card.Header>
				<Card.Title class="flex flex-col items-center text-3xl">
					<img src="/koroks/k_{korok.number}.png" alt="Korok {korok.number}" />
					<p>{found ? 'You have already found me' : 'You found a new Korok'}</p>
					<p>You have found {yourFinds} Korok{yourFinds === 1 ? '' : 's'}</p>
					<p>
						{korokFinds - 1} other {korokFinds - 1 === 1 ? 'person has' : 'people have'} found this Korok
					</p>
				</Card.Title>
				<Card.Action class="text-3xl">#{korok.number}</Card.Action>
			</Card.Header>
		</Card.Root>
	{/if}
</div>
