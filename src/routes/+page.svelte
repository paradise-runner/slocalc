<!-- App.svelte -->
<script>
	let sloTarget = 30;
	let errorBudgetConsumed = 2;
	let longWindow = 1;
    let sloTargetPercentage = 99.5;

	$: shortWindow = (longWindow / 12) * 60;
	$: burnRate = (sloTarget * 24 * errorBudgetConsumed) / (longWindow * 100);

</script>

<main class="container mx-auto p-4 max-w-lg">
	<h1 class="text-3xl font-bold mb-4">SLO Burn Rate Calculator</h1>

	<div class="mb-4">
		<label for="sloTarget" class="block mb-2">Length of SLO target (in days)</label>
		<input type="number" id="sloTarget" bind:value={sloTarget} class="w-full p-2 border rounded" />
	</div>

	<div class="mb-4">
		<label for="errorBudget" class="block mb-2">Percentage of error budget consumed</label>
		<input
			type="number"
			id="errorBudget"
			bind:value={errorBudgetConsumed}
			class="w-full p-2 border rounded"
		/>
	</div>

	<div class="mb-4">
		<label for="longWindow" class="block mb-2">Long window (in hours)</label>
		<input
			type="number"
			id="longWindow"
			bind:value={longWindow}
			class="w-full p-2 border rounded"
		/>
	</div>

    <div class="mb-4">
        <label for="sloTargetPercentage" class="block mb-2">SLO Target Percentage</label>
        <input
            type="number"
            id="sloTargetPercentage"
            bind:value={sloTargetPercentage}
            class="w-full p-2 border rounded"
        />

	<div class="mb-4">
		<label class="block mb-2">Short window (in minutes)</label>
		<input
			type="number"
			value={shortWindow}
			disabled
			class="w-full p-2 border rounded bg-gray-100"
		/>
	</div>


	{#if burnRate}
		<div class="mt-4 p-4 bg-gray-100 rounded">
			<h2 class="text-xl font-semibold mb-2">Result:</h2>
			<p>Burn Rate: {burnRate.toFixed(1)}</p>
		</div>
        <!-- SLO Target Percentage Check -->
        {#if burnRate > 1/(1-(sloTargetPercentage/100))}
            <div class="mt-4 p-4 bg-red-100 rounded">
                <h2 class="text-xl font-semibold mb-2">Result:</h2>
                <p>Burn Rate thershold is too high. </p>
                <p>Either raise the SLO Target Percentage or extend the Long Window</p>
                <p>Current Burn Rate: {burnRate.toFixed(1)}</p>
                <p>Burn Rate threshold: {(1/(1-(sloTargetPercentage/100))).toFixed(0)}</p>
            </div>
        {:else}
            <div class="mt-4 p-4 bg-green-100 rounded">
                <h2 class="text-xl font-semibold mb-2">Result:</h2>
                <p>Burn Rate threshold is valid for your SLO Target Percentage</p>
            </div>
        {/if}
	{/if}
</main>

<!-- In your project's CSS file or a style tag -->
<style>
	@import 'https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css';
</style>
