<script lang="ts">
	import Map from '#lib/components/map.svelte';
	import * as Dialog from '$lib/components/ui/dialog/';
	import Label from '#lib/components/ui/label/label.svelte';
	import Input from '#lib/components/ui/input/input.svelte';
	import Button, { buttonVariants } from '#lib/components/ui/button/button.svelte';
	import {
		addAreaAdmin,
		addKoroksAdmin,
		deleteAreaAdmin,
		deleteKoroksAdmin,
		getAreas,
		getKoroksAdmin,
		updateKoroksAdmin
	} from '../query/korok.remote';
	import * as Card from '$lib/components/ui/card';
	import Switch from '#lib/components/ui/switch/switch.svelte';
	import { generateQRCode, tripleNumber } from '$lib/utils';
	let areas = getAreas();
	let markers = getKoroksAdmin();
	let openKorok = $state(false);
	let nextNumber = $state(1);
	let changeId = $state('');
	let newKorok: {
		lat: number;
		lng: number;
		number: number;
		description: string;
		type: number;
		release: number;
		isRelease: boolean;
	} = $state({
		description: '',
		lat: 0,
		lng: 0,
		number: 0,
		title: '',
		release: 0,
		type: 0,
		isRelease: false
	});
</script>

<h1 class="m-2 text-5xl">Korok Hunt</h1>
<div class="flex flex-col gap-4 p-8">
	<Map
		markers={await markers}
		areas={await areas}
		onNewArea={async (area) => {
			await addAreaAdmin({
				color: area.color,
				points: area.points
			});
			await areas.refresh();
		}}
		onDeleteArea={async (id) => {
			await deleteAreaAdmin({ id });
			await areas.refresh();
		}}
		onDeleteKorok={async (id) => {
			await deleteKoroksAdmin({ id });
			await markers.refresh();
		}}
		onNewKorok={(area) => {
			openKorok = true;
			newKorok = {
				description: '',
				lat: area[0],
				lng: area[1],
				number: nextNumber,
				release: 0,
				type: 0,
				isRelease: false
			};
			nextNumber++;
		}}
		isAdmin
	/>
</div>

<Dialog.Root bind:open={openKorok}>
	<Dialog.Content>
		<Dialog.Header>
			<Dialog.Title>Create Korok</Dialog.Title>
		</Dialog.Header>
		{#if changeId}
			<Label>Korok ID: {changeId}</Label>
		{/if}
		<Label for="number">Number</Label>
		<Input id="number" type="number" bind:value={newKorok.number} />
		<Label for="desc">Description</Label>
		<Input id="desc" bind:value={newKorok.description} />
		<Label for="lat">Latatude</Label>
		<Input id="lat" type="number" bind:value={newKorok.lat} />
		<Label for="lat">Longitude</Label>
		<Input id="lng" type="number" bind:value={newKorok.lng} />
		<Label for="type">Type</Label>
		<Input id="type" type="number" bind:value={newKorok.type} />
		<Label for="release">Release</Label>
		<Input id="release" type="number" bind:value={newKorok.release} />
		<Label for="isRelease">Is Released</Label>
		<Switch id="isRelease" type="checkbox" bind:checked={newKorok.isRelease} />

		<Dialog.Footer class="sm:justify-start">
			{#if changeId}
				<Dialog.Close
					onclick={async () => {
						await updateKoroksAdmin({
							...newKorok,
							id: changeId
						});
						await markers.refresh();
						changeId = '';
					}}
					class={buttonVariants({ variant: 'default' })}>Update</Dialog.Close
				>
			{:else}
				<Dialog.Close
					onclick={async () => {
						await addKoroksAdmin({
							...newKorok
						});
						await markers.refresh();
						changeId = '';
					}}
					class={buttonVariants({ variant: 'default' })}>Create</Dialog.Close
				>
			{/if}
		</Dialog.Footer>
	</Dialog.Content>
</Dialog.Root>

<div class="flex flex-col gap-4 p-8">
	<Card.Root class="p-8">
		{#each await markers as korok (korok.id)}
			<Card.Root class="gap-2 p-2 text-3xl">
				<Card.Content class="flex content-center gap-2">
					<p>#{tripleNumber(korok.number)}</p>
					<p class="text-xl">{korok.description}</p>
					<div class="grow"></div>
					<Button
						onclick={() => {
							openKorok = true;
							newKorok = {
								...korok
							};
							changeId = korok.id;
						}}>Edit</Button
					>
					<Button
						onclick={async () => {
							await generateQRCode({ id: korok.id, type: korok.type, number: korok.number });
						}}>Export card</Button
					>
				</Card.Content>
			</Card.Root>
		{/each}
	</Card.Root>
</div>
