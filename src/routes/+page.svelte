<script>
	import DarkModeToggle from './DarkModeToggle.svelte';
	import '../app.css';

	let sloTarget = 30;
	let sloTargetPercentage = 99.5;
	let activeTab = 'burn-rate';

	let errorBudgetConsumed = 2;
	let longWindow = 1;

	let totalRequests = 100;
	let totalRequestsScale = 1000;
	const scaleOptions = [
		{ value: 1, label: 'ones' },
		{ value: 10, label: 'tens' },
		{ value: 100, label: 'hundreds' },
		{ value: 1000, label: 'thousands' },
		{ value: 1000000, label: 'millions' },
		{ value: 1000000000, label: 'billions' }
	];

	const tabs = [
        { id: 'burn-rate', label: 'Burn Rate' },
        { id: 'error-budget', label: 'Error Budget' },
        { id: 'downtime', label: 'Downtime' }
    ];

	$: shortWindow = (longWindow / 12) * 60;
	$: burnRate = (sloTarget * 24 * errorBudgetConsumed) / (longWindow * 100);

	let timeUnit = 'month';

	$: allowedErrors = Math.floor(
		totalRequests * totalRequestsScale * (1 - sloTargetPercentage / 100)
	);
	$: errorBudget = (allowedErrors / (totalRequests * totalRequestsScale)) * 100;

	$: allowedDowntime = calculateAllowedDowntime(sloTargetPercentage, timeUnit);

	function setActiveTab(tab) {
		activeTab = tab;
	}

	function formatNumber(num) {
		return num.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ',');
	}

	function calculateAllowedDowntime(sloTargetPercentage, unit) {
		const availableMinutes = (100 - sloTargetPercentage) / 100;
		let totalMinutes;

		switch (unit) {
			case 'week':
				totalMinutes = 7 * 24 * 60;
				break;
			case 'month':
				totalMinutes = 30 * 24 * 60;
				break;
			case 'quarter':
				totalMinutes = 91 * 24 * 60;
				break;
			case 'year':
				totalMinutes = 365 * 24 * 60;
				break;
			default:
				totalMinutes = 24 * 60;
		}

		return (availableMinutes * totalMinutes).toFixed(0);
	}
</script>

<main
	class="container mx-auto p-4 max-w-lg dark:text-white transition-colors duration-100"
>
	<div class="flex justify-between items-center mb-4">
		<h1 class="text-3xl font-bold text-center dark:text-white">SLO Calculator</h1>
		<DarkModeToggle />
	</div>
	<div class="mb-4">
		<label for="sloTarget" class="block mb-2 dark:text-gray-300"
			>Length of SLO target (in days)</label
		>
		<input
			type="number"
			id="sloTarget"
			bind:value={sloTarget}
			class="w-full p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
		/>
	</div>

	<div class="mb-4">
		<label for="sloTargetPercentage" class="block mb-2 dark:text-gray-300"
			>SLO Target Percentage</label
		>
		<input
			type="number"
			id="sloTargetPercentage"
			bind:value={sloTargetPercentage}
			class="w-full p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
		/>
	</div>

	<div class="flex justify-center mb-4">
		<ul class="bg-gray-100 dark:bg-gray-700 py-3 px-2 rounded-lg inline-flex">
			{#each tabs as tab}
				<li>
					<a
						class="px-4 py-2 rounded-lg transition-colors duration-150 {activeTab === tab.id
							? 'bg-white dark:bg-gray-800 dark:text-white shadow-sm'
							: 'dark:text-white'}"
						href={`#${tab.id}`}
						on:click|preventDefault={() => setActiveTab(tab.id)}
					>
						{tab.label}
					</a>
				</li>
			{/each}
		</ul>
	</div>

	{#if activeTab === 'burn-rate'}
		<div class="mb-4">
			<label for="errorBudget" class="block mb-2 dark:text-gray-300"
				>Percentage of error budget consumed</label
			>
			<input
				type="number"
				id="errorBudget"
				bind:value={errorBudgetConsumed}
				class="w-full p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
			/>
		</div>

		<div class="mb-4">
			<label for="longWindow" class="block mb-2 dark:text-gray-300">Long window (in hours)</label>
			<input
				type="number"
				id="longWindow"
				bind:value={longWindow}
				class="w-full p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
			/>
		</div>

		<div class="mb-4">
			<label class="block mb-2 dark:text-gray-300">Short window (in minutes)</label>
			<input
				type="number"
				value={shortWindow.toFixed(2)}
				disabled
				class="w-full p-2 border rounded bg-gray-100 dark:bg-gray-600 dark:border-gray-700 dark:text-gray-300"
			/>
		</div>

		{#if burnRate}
			<div class="mt-4 p-4 bg-gray-100 dark:bg-gray-700 rounded">
				<h2 class="text-xl font-semibold mb-2 dark:text-white">Result:</h2>
				<p class="dark:text-gray-300">Burn Rate: {burnRate.toFixed(1)}</p>
			</div>
			{#if burnRate > 1 / (1 - sloTargetPercentage / 100)}
				<div class="mt-4 p-4 bg-red-100 dark:bg-red-900 rounded">
					<h2 class="text-xl font-semibold mb-2 dark:text-red-100">Warning:</h2>
					<p class="dark:text-red-200">Burn Rate threshold is too high.</p>
					<p class="dark:text-red-200">
						Either raise the SLO Target Percentage or extend the Long Window
					</p>
					<p class="dark:text-red-200">Current Burn Rate: {burnRate.toFixed(1)}</p>
					<p class="dark:text-red-200">
						Burn Rate threshold: {(1 / (1 - sloTargetPercentage / 100)).toFixed(0)}
					</p>
				</div>
			{:else}
				<div class="mt-4 p-4 bg-green-100 dark:bg-green-900 rounded">
					<h2 class="text-xl font-semibold mb-2 dark:text-green-100">Result:</h2>
					<p class="dark:text-green-200">
						Burn Rate threshold is valid for your SLO Target Percentage
					</p>
				</div>
			{/if}
		{/if}
	{:else if activeTab === 'error-budget'}
		<div class="mb-4">
			<label for="totalRequests" class="block mb-2 dark:text-gray-300">
				Total number of requests
			</label>
			<div class="flex space-x-2">
				<input
					type="number"
					id="totalRequests"
					bind:value={totalRequests}
					min="1"
					max="100"
					class="w-2/3 p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
				/>
				<select
					bind:value={totalRequestsScale}
					class="w-1/3 p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
				>
					{#each scaleOptions as option}
						<option value={option.value}>{option.label}</option>
					{/each}
				</select>
			</div>
		</div>

		<div class="mt-4 p-4 bg-gray-100 dark:bg-gray-700 rounded">
			<h2 class="text-xl font-semibold mb-2 dark:text-white">Error Budget Estimation:</h2>
			<p class="dark:text-gray-300">
				Total Requests: {formatNumber(totalRequests * totalRequestsScale)}
			</p>
			<p class="dark:text-gray-300">Allowed Errors: {formatNumber(allowedErrors)}</p>
			<p class="dark:text-gray-300">Error Budget: {errorBudget.toFixed(2)}%</p>
		</div>
	{:else if activeTab === 'downtime'}
		<div class="mb-4">
			<label for="timeUnit" class="block mb-2 dark:text-gray-300">Time Unit</label>
			<select
				bind:value={timeUnit}
				id="timeUnit"
				class="w-full p-2 border rounded dark:bg-gray-700 dark:border-gray-600 dark:text-white"
			>
				<option value="day">Day</option>
				<option value="week">Week</option>
				<option value="month">Month</option>
				<option value="quarter">Quarter</option>
				<option value="year">Year</option>
			</select>
		</div>

		<div class="mt-4 p-4 bg-gray-100 dark:bg-gray-700 rounded">
			<h2 class="text-xl font-semibold mb-2 dark:text-white">Allowed Downtime:</h2>
			<p class="dark:text-gray-300">{allowedDowntime} minutes per {timeUnit}</p>
			<p class="dark:text-gray-300">{(allowedDowntime / 60).toFixed(2)} hours per {timeUnit}</p>
		</div>
	{/if}

	<div class="mt-8 text-center text-sm text-gray-600 dark:text-gray-400">
		<div class="flex justify-center space-x-4">
			<div>
				<a
					href="https://hec.works"
					target="_blank"
					rel="noopener noreferrer"
					class="hover:text-gray-900 dark:hover:text-gray-200"
				>
					Visit hec.works 🚀
				</a>
				<p class="mt-2">For more projects and other work</p>
			</div>
			<div>
				<a
					href="https://github.com/paradise-runner/slocalc"
					target="_blank"
					rel="noopener noreferrer"
					class="hover:text-gray-900 dark:hover:text-gray-200"
				>
					View on GitHub ❤️
				</a>
				<p class="mt-2">Open source and free to use!</p>
			</div>
		</div>
	</div>
</main>

<style>
	@import 'https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css';
</style>
