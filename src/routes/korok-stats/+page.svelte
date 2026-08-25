<script>
	import { tripleNumber } from '$lib/utils';
	import * as Card from '$lib/components/ui/card/';
	import { getKorokFinds } from '../query/korok.remote';

	let koroks = await getKorokFinds();
	let sortedKoroks = $derived([...koroks].sort((a, b) => b.findCount - a.findCount));
</script>

<div class="mx-auto max-w-4xl px-4 py-8">
	<!-- Header -->
	<div class="mb-8 text-center">
		<h1 class="text-5xl font-black tracking-tight text-foreground">Korok Leaderboard</h1>

		<p class="mt-2 text-lg text-muted-foreground">The most discovered Koroks</p>
	</div>

	<!-- Leaderboard -->
	<Card.Root class="overflow-hidden border-2 border-border bg-card pt-0 shadow-lg">
		<Card.Header class="border-b-2 border-border bg-secondary/60 px-6 py-5">
			<div class="flex items-center justify-between">
				<div>
					<Card.Title class="text-2xl font-black">Korok Rankings</Card.Title>
					<Card.Description class="mt-1">Ranked by number of discoveries</Card.Description>
				</div>

				<div class="rounded-full border-2 border-border bg-background px-4 py-2 font-bold">
					{koroks.length} Koroks
				</div>
			</div>
		</Card.Header>

		<Card.Content class="p-4 sm:p-6">
			<div class="flex flex-col gap-3">
				{#each sortedKoroks as korok, index (korok.korok.id)}
					{@const rank = index + 1}

					<div
						class="group relative overflow-hidden rounded-xl border-2 border-border/70 bg-background p-4 transition-all duration-200 hover:-translate-y-0.5 hover:border-primary hover:shadow-md"
					>
						<div class="relative flex items-center gap-4">
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
								<img class="w-4" src={`/koroks/k_${korok.korok.number}.png`} alt="" />
							</div>

							<!-- Korok number -->
							<div class="min-w-0 flex-1">
								<p class="text-sm font-bold tracking-wider text-muted-foreground uppercase">
									Korok
								</p>

								<p class="text-2xl font-black text-foreground">
									#{tripleNumber(korok.korok.number)}
								</p>
							</div>

							<!-- Finds -->
							<div class="text-right">
								<p class="text-2xl font-black text-primary">
									{korok.findCount}
								</p>

								<p class="text-sm font-semibold text-muted-foreground">
									{korok.findCount === 1 ? 'find' : 'finds'}
								</p>
							</div>
						</div>
					</div>
				{/each}
			</div>
		</Card.Content>
	</Card.Root>
</div>
