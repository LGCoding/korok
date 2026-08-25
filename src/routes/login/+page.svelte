<script lang="ts">
	import { Label } from '$lib/components/ui/label/';
	import { enhance } from '$app/forms';
	import type { ActionData } from './$types';
	import Input from '#lib/components/ui/input/input.svelte';
	import Button from '#lib/components/ui/button/button.svelte';
	import * as Card from '$lib/components/ui/card/';
	import * as Tabs from '$lib/components/ui/tabs/';
	import type { Area } from '#lib/components/map.svelte';

	let { form }: { form: ActionData } = $props();
</script>

<form method="post" action="?/signInEmail" use:enhance>
	<Card.Root class="m-10">
		<Tabs.Root value="login">
			<Card.Header>
				<Tabs.List>
					<Tabs.Trigger value="login">Login</Tabs.Trigger>
					<Tabs.Trigger value="register">Register</Tabs.Trigger>
				</Tabs.List>
			</Card.Header>
			<Card.Content class="flex flex-col gap-2">
				<Label for="email">Email</Label>
				<Input id="email" type="email" name="email" />
				<Label for="password">Password</Label>
				<Input id="password" type="password" name="password" />

				<Tabs.Content class="flex flex-col gap-2" value="login">
					<p class="text-red-500">{form?.message ?? ''}</p>
					<Button type="submit">Login</Button>
				</Tabs.Content>
				<Tabs.Content class="flex flex-col gap-2" value="register">
					<Label for="name">Username</Label>
					<Input id="name" name="name" />
					<p class="text-red-500">{form?.message ?? ''}</p>
					<Button type="submit" formaction="?/signUpEmail">Register</Button>
				</Tabs.Content>
			</Card.Content>
			<Card.Footer></Card.Footer>
		</Tabs.Root>
	</Card.Root>
</form>
