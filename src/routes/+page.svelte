<script>
	import { onMount } from 'svelte';

	let data = {
		date: '',
		total_intake: 0,
		total_expenditure: 0,
		initial_goal: 0,
		previous_day_calorie_surplus: 0,
		adjusted_goal: 0,
		remaining_calories: 0,
		todays_intakes: [] // Added to hold today's intakes
	};

	onMount(async () => {
		const summaryResponse = await fetch('http://127.0.0.1:8000/energy/daily_summary');
		if (summaryResponse.ok) {
			const summaryJson = await summaryResponse.json();
			data = {
				...data,
				...summaryJson
			};
		} else {
			console.error('Failed to fetch daily summary data:', summaryResponse.status);
		}

		const intakesResponse = await fetch('http://127.0.0.1:8000/energy/intakes/todays_intakes');
		if (intakesResponse.ok) {
			const intakesJson = await intakesResponse.json();
			data.todays_intakes = intakesJson; // Assign fetched intakes to data.todays_intakes
		} else {
			console.error("Failed to fetch today's intakes data:", intakesResponse.status);
		}
	});
</script>

<h1 class="mt-5 text-center text-2xl">Energy Tracker</h1>
<div class="mt-3 text-center">
	<p>Date: {data.date}</p>
	<p>Total Intake: {data.total_intake.toLocaleString()}</p>
	<p>Expenditure: {data.total_expenditure.toLocaleString()}</p>
	<p>Initial Goal: {data.initial_goal.toLocaleString()}</p>
	<p>Previous Day Calorie Surplus: {data.previous_day_calorie_surplus.toLocaleString()}</p>
	<p>Adjusted Goal: {data.adjusted_goal.toLocaleString()}</p>
	<p class="font-bold">Remaining Calories: {data.remaining_calories.toLocaleString()}</p>
	{#if data.todays_intakes && data.todays_intakes.length > 0}
		<h2 class="mt-5 text-center text-xl">Today's Intakes</h2>
		<div class="my-4 text-center">
			<button
				class="rounded bg-blue-500 px-4 py-2 font-bold text-white hover:bg-blue-700"
				on:click={() => {
					data.todays_intakes = [
						...data.todays_intakes.sort((a, b) => new Date(a.timestamp) - new Date(b.timestamp))
					];
				}}>Sort by Time</button
			>
			<button
				class="rounded bg-green-500 px-4 py-2 font-bold text-white hover:bg-green-700"
				on:click={() => {
					data.todays_intakes = [...data.todays_intakes.sort((a, b) => b.calories - a.calories)];
				}}>Sort by Calories</button
			>
		</div>
		<div class="flex flex-col items-center">
			{#each data.todays_intakes as intake}
				<div class="flex w-1/4 justify-between">
					<p>{intake.label}</p>
					<p>{intake.calories.toLocaleString()} calories</p>
				</div>
			{/each}
		</div>
	{/if}
</div>
