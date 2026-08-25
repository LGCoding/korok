<script>
	import { tripleNumber } from '$lib/utils';
	import * as Card from '$lib/components/ui/card/';
	import { getKorokFinds } from '../query/korok.remote';
	let koroks = await getKorokFinds();
</script>

<h1 class="m-2 text-5xl">Leaderboard</h1>
<div class="flex flex-col gap-4 p-8">
	<Card.Root class="p-8">
		{#each koroks as korok (korok.korok.id)}
			<Card.Root class="gap-2 bg-sidebar-accent p-2 text-xl">
				<Card.Content class="flex justify-between gap-2">
					<p>#{tripleNumber(korok.korok.number)}</p>
					<p>Found {korok.findCount} times</p>
				</Card.Content>
			</Card.Root>
		{/each}
	</Card.Root>
</div>
