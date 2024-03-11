<script>
	import { onMount } from 'svelte';

	let data = {
		date: new Date(),
		total_calories_consumed: 0,
		total_expenditure: 0,
		calorie_goal: 0,
		remaining_calories_until_goal: 0
	};
	onMount(async () => {
		const dailySumResponse = await fetch('http://127.0.0.1:8000/energy/daily_summary');
		if (dailySumResponse.ok) {
			const dailySumJson = await dailySumResponse.json();
			data = dailySumJson;
		} else {
			console.error('Failed to fetch daily sum data:', dailySumResponse.status);
		}
	});
</script>

<h1 class="mt-5 text-center text-2xl">Energy Tracker</h1>
<div class="mt-3 text-center">
	<p>Date: {new Date(data.date).toLocaleDateString()}</p>
	<p>Total Calories Consumed: {data.total_calories_consumed.toLocaleString()}</p>
	<p>Expenditure: {data.total_expenditure.toLocaleString()}</p>
	<p>Adjusted Goal: {data.calorie_goal.toLocaleString()}</p>
	<p class="font-bold">Remaining Calories: {data.remaining_calories_until_goal.toLocaleString()}</p>
	{#if data.todays_intakes}
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
					<p>{intake.calories.toLocaleString()}</p>
				</div>
			{/each}
		</div>
	{/if}
</div>
