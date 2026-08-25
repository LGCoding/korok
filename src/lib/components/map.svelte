<script lang="ts" module>
	export type Area = {
		points: [number, number][];
		color: string;
		id: number;
	};
	export type MarkerK = { lng: number; lat: number; number: number; id: string };
</script>

<script lang="ts">
	import { browser } from '$app/env';
	import type {
		Icon,
		IconOptions,
		Marker,
		Map,
		LeafletMouseEventHandlerFn,
		Polyline,
		Polygon
	} from 'leaflet';
	import type * as geojson from 'geojson';
	import { onMount } from 'svelte';
	import * as Card from './ui/card/';
	import { Button } from './ui/button';
	import { cn } from '$lib/utils';
	let element: HTMLElement;
	let {
		markers,
		areas,
		isAdmin,
		onNewArea,
		onDeleteArea,
		onNewKorok,
		onDeleteKorok
	}: {
		markers: MarkerK[];
		areas?: Area[];
		isAdmin?: boolean;
		onNewArea?: (area: Area) => void;
		onDeleteArea?: (id: number) => void;
		onNewKorok?: (pos: [number, number]) => void;
		onDeleteKorok?: (id: string) => void;
	} = $props();
	let L: typeof import('leaflet');
	let iconNormal: Icon<IconOptions>;
	let iconAdd: Icon<IconOptions>;
	let mapMarkers: Marker<unknown>[] = [];
	let map: Map;
	let mounted = $state(false);
	let clickMode: '' | 'new-korok' | 'new-area' = $state('');
	let newCurrentArea: {
		points: [number, number][];
		closed: boolean;
		color: string;
	} = $state({
		points: [],
		closed: false,
		color: 'false'
	});
	let newPolyline: Polyline<geojson.LineString | geojson.MultiLineString, unknown>;
	let newPoints: Marker<unknown>[] = [];

	let areasPolys: Polygon<unknown>[] = [];

	let polyContext:
		| {
				open: boolean;
				x: number;
				y: number;
				id: number;
				type: 'area';
		  }
		| {
				open: boolean;
				x: number;
				y: number;
				id: string;
				type: 'korok';
		  } = $state({
		open: false,
		x: 0,
		y: 0,
		id: 0,
		type: 'area'
	});

	onMount(async () => {
		if (browser) {
			L = await import('leaflet');
			map = L.map(element, { scrollWheelZoom: false }).setView(
				[42.73035819852768, -73.67996530989872],
				15
			);
			map.on('click', onMapClick);
			iconNormal = L.icon({
				iconUrl: 'map-pin.svg',

				iconSize: [24, 24], // size of the icon
				iconAnchor: [12, 23], // point of the icon which will correspond to marker's location
				popupAnchor: [0, -23] // point from which the popup should open relative to the iconAnchor
			});
			iconAdd = L.icon({
				iconUrl: 'map-pin-plus.svg',

				iconSize: [24, 24], // size of the icon
				iconAnchor: [12, 23], // point of the icon which will correspond to marker's location
				popupAnchor: [0, -23] // point from which the popup should open relative to the iconAnchor
			});
			L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution:
					'&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
			}).addTo(map);
			mounted = true;
		}
	});
	$effect(() => {
		if (!browser || !mounted) return;
		for (let marker of mapMarkers) {
			marker.removeFrom(map);
		}
		for (let marker of markers) {
			let m = L.marker([marker.lat, marker.lng], {
				icon: iconNormal,
				title: marker.number + ''
			}).addTo(map);
			if (isAdmin) {
				m.addEventListener('contextmenu', (e) => {
					polyContext = {
						open: true,
						x: e.originalEvent.clientX,
						y: e.originalEvent.clientY,
						id: marker.id,
						type: 'korok'
					};
				});
			}
			mapMarkers.push(m);
		}
	});
	$effect(() => {
		if (!browser || !mounted) return;
		for (let area of areasPolys) {
			area.removeFrom(map);
		}
		if (!areas) return;
		for (let area of areas) {
			let poly = L.polygon(area.points, { interactive: true }).addTo(map);
			if (isAdmin) {
				poly.addEventListener('contextmenu', (e) => {
					polyContext = {
						open: true,
						x: e.originalEvent.clientX,
						y: e.originalEvent.clientY,
						id: area.id,
						type: 'area'
					};
				});
			}
			areasPolys.push(poly);
		}
	});
	$effect(() => {
		if (!browser || !mounted) return;
		if (newPolyline) newPolyline.removeFrom(map);
		newPolyline = L.polyline(newCurrentArea.points, { color: 'red', fill: true }).addTo(map);
		for (let point of newPoints) {
			point.removeFrom(map);
		}
		if (!newCurrentArea.closed) {
			for (let point of newCurrentArea.points) {
				newPoints.push(
					L.marker(point, { icon: iconAdd })
						.addEventListener('click', (e) => {
							newCurrentArea.points.push([e.latlng.lat, e.latlng.lng]);
							if (onNewArea) {
								onNewArea({
									color: newCurrentArea.color,
									points: newCurrentArea.points,
									id: 0
								});
							}
							newCurrentArea = {
								closed: false,
								color: '',
								points: []
							};
							clickMode = '';
						})
						.addTo(map)
				);
			}
		}
	});
	let onMapClick: LeafletMouseEventHandlerFn = (e) => {
		polyContext.open = false;
		if (isAdmin && clickMode === 'new-area') {
			newCurrentArea.points.push([e.latlng.lat, e.latlng.lng]);
		}
		if (isAdmin && clickMode === 'new-korok') {
			if (onNewKorok) onNewKorok([e.latlng.lat, e.latlng.lng]);
		}
	};
</script>

<div class="flex flex-col items-center">
	<div
		class={cn('container h-100 w-full', {
			// 'cursor-crosshair': clickMode === 'new-area' || clickMode === 'new-korok'
		})}
		bind:this={element}
	></div>
	{#if polyContext.open}
		<Card.Root
			style="left:{polyContext.x ?? 0}px; top: {polyContext.y ?? 0}px;"
			class="absolute z-1000 flex flex-row rounded p-2"
		>
			<Button
				variant="ghost"
				onclick={() => {
					if (polyContext.type === 'area' && onDeleteArea) {
						onDeleteArea(polyContext.id);
						polyContext.open = false;
					} else if (polyContext.type === 'korok' && onDeleteKorok) {
						onDeleteKorok(polyContext.id);
						polyContext.open = false;
					}
				}}>Delete</Button
			>
		</Card.Root>
	{/if}
	{#if isAdmin}
		<Card.Root class="flex flex-row rounded-none p-2">
			Mode: {clickMode}
			<Button
				onclick={() => {
					clickMode = 'new-area';
					newCurrentArea.closed = false;
					newCurrentArea.points = [];
					newCurrentArea.color = 'rgba(22, 234, 237, 0.23)';
				}}>New Area</Button
			>
			<Button
				onclick={() => {
					clickMode = 'new-korok';
				}}>New Korok</Button
			>
		</Card.Root>
	{/if}
</div>

<style>
	.container :global(.leaflet-pane) {
		z-index: auto !important;
	}
</style>
