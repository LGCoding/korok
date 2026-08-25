<script>
	import * as Card from '$lib/components/ui/card/';
	import { Trophy } from 'lucide-svelte';
	import { getUserFinds } from '../query/korok.remote';

	let players = await getUserFinds();
	let sortedPlayers = $derived([...players].sort((a, b) => b.koroksFound - a.koroksFound));
</script>

<div class="mx-auto max-w-4xl px-4 py-8">
	<!-- Header -->
	<div class="mb-8 text-center">
		<h1 class="text-5xl font-black tracking-tight text-foreground">Player Leaderboard</h1>

		<p class="mt-2 text-lg text-muted-foreground">The greatest Korok hunters</p>
	</div>

	<!-- Leaderboard -->
	<Card.Root class="overflow-hidden border-2 border-border bg-card pt-0 shadow-lg">
		<Card.Header class="border-b-2 border-border bg-secondary/60 px-6 py-5">
			<div class="flex items-center justify-between">
				<div>
					<Card.Title class="text-2xl font-black"
						><Trophy class="inline" /> Top Korok Hunters</Card.Title
					>

					<Card.Description class="mt-1">Ranked by Koroks discovered</Card.Description>
				</div>

				<div class="rounded-full border-2 border-border bg-background px-4 py-2 font-bold">
					{players.length} Hunters
				</div>
			</div>
		</Card.Header>

		<Card.Content class="p-4 sm:p-6">
			<div class="flex flex-col gap-3">
				{#each sortedPlayers as player, index (player.user.id)}
					{@const rank = index + 1}

					<div
						class={`group relative overflow-hidden rounded-xl border-2 p-4 transition-all duration-200 hover:-translate-y-0.5 hover:shadow-md ${
							rank === 1
								? 'border-yellow-600/60 bg-yellow-400/10'
								: rank === 2
									? 'border-slate-400/60 bg-slate-300/10'
									: rank === 3
										? 'border-orange-700/60 bg-orange-400/10'
										: 'border-border/70 bg-background hover:border-primary'
						}`}
					>
						<div class="flex items-center gap-4">
							<!-- Rank -->
							<div
								class={`flex size-12 shrink-0 items-center justify-center rounded-full border-2 font-black ${
									rank === 1
										? 'border-yellow-600 bg-yellow-400/30 text-yellow-800'
										: rank === 2
											? 'border-slate-400 bg-slate-300/40 text-slate-700'
											: rank === 3
												? 'border-orange-700 bg-orange-400/30 text-orange-800'
												: 'border-border bg-card text-muted-foreground'
								}`}
							>
								{#if rank === 1}
									🥇
								{:else if rank === 2}
									🥈
								{:else if rank === 3}
									🥉
								{:else}
									#{rank}
								{/if}
							</div>

							<!-- Player -->
							<div class="min-w-0 flex-1">
								<p class="truncate text-xl font-black text-foreground">
									{player.user.name}
								</p>

								{#if player.lastFoundAt}
									<p class="mt-0.5 text-sm text-muted-foreground">
										Last discovery:
										{player.lastFoundAt.toLocaleString()}
									</p>
								{:else}
									<p class="mt-0.5 text-sm text-muted-foreground">No discoveries yet</p>
								{/if}
							</div>

							<!-- Score -->
							<div class="shrink-0 text-right">
								<p class="text-3xl font-black text-primary">
									{player.koroksFound}
								</p>

								<p class="text-sm font-semibold text-muted-foreground">
									{player.koroksFound === 1 ? 'Korok' : 'Koroks'}
								</p>
							</div>
						</div>
					</div>
				{/each}
			</div>
		</Card.Content>
	</Card.Root>
</div>
