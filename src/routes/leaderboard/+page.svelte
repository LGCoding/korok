<script>
	import * as Card from '$lib/components/ui/card/';
	import { getUserFinds } from '../query/korok.remote';
	let players = await getUserFinds();
</script>

<h1 class="m-2 text-5xl">Leaderboard</h1>
<div class="flex flex-col gap-4 p-8">
	<Card.Root class="p-8">
		{#each players as player, i (player.user.id)}
			<Card.Root class="gap-2 bg-sidebar-accent p-2 text-xl">
				<Card.Content class="flex justify-between gap-2">
					<p>{i + 1}. {player.user.name}</p>
					<p>{player.koroksFound} Koroks</p>
				</Card.Content>
				<Card.Footer class="ml-4! text-sm"
					>Last found: {player.lastFoundAt?.toLocaleString()}</Card.Footer
				>
			</Card.Root>
		{/each}
	</Card.Root>
</div>
