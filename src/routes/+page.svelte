<script lang="ts">
	import Map from '#lib/components/map.svelte';
	import * as Card from '$lib/components/ui/card/';
	import { getAreas, getKoroks } from './query/korok.remote';
	let areas = getAreas();
	let markers = getKoroks();
</script>

<h1 class="m-2 text-5xl">Korok Hunt</h1>
<div class="flex flex-col gap-4 p-8">
	<Card.Root class="">
		<Card.Header class=" text-3xl">What Is This?</Card.Header>

		<Card.Content>
			The RPI Korok Hunt is a scavenger hunt in which you find the Korok stickers hidden all over
			campus! Each Korok has a QR code to scan, which will increment your score. Compete against
			other students to find the most Koroks! The top three players by the end of May 3rd will win a
			Korok plushie! All Koroks will be found outside, and within the bounds shown above. Don't go
			searching anywhere you shouldn't be; Koroks probably aren't hiding there. For more info, take
			a look at the FAQ. Make sure to join our discord server for announcements, conversation,
			and/or questions about the event!
		</Card.Content>
	</Card.Root>
	<Map markers={await markers} areas={await areas} />
	<Card.Root class="">
		<Card.Header class=" text-xl">This is an example of what to look out for on campus:</Card.Header
		>

		<Card.Content>
			<img class="rounded-2xl" src="sticker_example.png" alt="example" />
		</Card.Content>
	</Card.Root>
	<Card.Root class="">
		<Card.Header class=" text-3xl">Faq?</Card.Header>

		<Card.Content>
			<b>What types of places can I find Koroks?</b>
			<br />
			Koroks can be found pretty much anywhere outdoors on campus within the bounds on the map above,
			aside from a few types of places (see below).
			<br /><br />

			<b>What types of places can I NOT find Koroks?</b>
			<br />
			There won't be any Koroks in restricted, public, indoor, or too dangerous areas. That includes construction
			areas, public roads and sidewalks, and any places you're clearly not allowed to be in. Additionally,
			you should not have to travel into or through any of these types of areas in order to reach a Korok.
			<br /><br />

			<b>What types of objects can Koroks be placed on?</b>
			<br />
			Koroks are placed on things that a sticker can actually stick to (like metal, glass, or other kinds
			of smooth surfaces). Stickers don't stick well to rough or dirty surfaces like bricks or bark. Also,
			Koroks are only placed on static, non-moving objects. They won't be placed on things that would
			reasonably be expected to move around.
			<br /><br />

			<b>Can Koroks be found on roofs, balconies, or any other such places?</b>
			<br />
			All Koroks can be found without stepping a foot indoors. If there isn't a way to get somewhere without
			going inside, then a Korok won't be there.
			<br /><br />

			<b>Do I have to look underneath anything?</b>
			<br />
			Koroks won't be placed on any surfaces facing towards the ground. That means you don't have to look
			on the underside of every table and bench on campus, for example. In general, all Koroks should
			be able to be seen in some way without being crouched, but that doesn't mean you won't have to crouch
			in order to actually scan a Korok or to reach certain areas.
			<br /><br />

			<b>Is the parking garage in bounds?</b>
			<br />
			Nope, we're counting it as indoors.
			<br /><br />

			<b>Can I share hints or locations of Koroks with others?</b>
			<br />
			The challenge of this event is searching and finding the Koroks yourself, so giving people hints
			or locations hurts competition and is unfair to those searching without help. However, it's fine
			to share locations while actively searching with others (just remember, you're still competing with
			them on the leaderboard). Just don't broadcast hints or Korok locations to other people, in order
			to keep things fair. We just don't want a situation where the top players share hints with each
			other, meaning that no one else could possibly compete by searching fairly.
			<br /><br />

			<b>Why did the Korok I scanned throw an error?</b>
			<br />
			It could be an internet issue or it could be that it wasn't scanned properly. Make sure to try scanning
			again. If you continue to have issues, feel free to ask for help on our
			<u
				><a target="_blank" rel="noopener noreferrer" href="https://discord.gg/A79Au6BcsJ"
					>discord server</a
				></u
			>.
			<br />
			Additionally, you'll get an error when scanning Koroks from previous events that weren't cleaned
			up. Make sure the sticker you're scanning has the 3rd RPI Korok Hunt logo and matches in size with
			most of the other stickers. If you find any old Koroks, let us know on our discord server.
			<br /><br />
		</Card.Content>
	</Card.Root>

	<Card.Root class="">
		<Card.Header class=" text-3xl">Cheating Notice</Card.Header>

		<Card.Content>
			Don't ruin the fun for everyone else. Removing or moving Koroks from their original location,
			as well as scanning Koroks that have been removed will result in disqualification.
		</Card.Content>
	</Card.Root>
</div>
