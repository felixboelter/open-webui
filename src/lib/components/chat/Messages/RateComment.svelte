<script lang="ts">
	import { toast } from 'svelte-sonner';

	import { createEventDispatcher, onMount, getContext } from 'svelte';

	const i18n = getContext('i18n');

	const dispatch = createEventDispatcher();

	export let messageId = null;
	export let show = false;
	export let message;

	let LIKE_REASONS = [];
	let DISLIKE_REASONS = [];

	function loadReasons() {
		LIKE_REASONS = [
			$i18n.t('Korrekte Lösung'),
			$i18n.t('Klare Erklärung der Schritte'),
			$i18n.t('Effiziente Methode verwendet'),
			$i18n.t('Innovativer Ansatz'),
			$i18n.t('Gute Problemlösungsstrategie'),
			$i18n.t('Aufmerksamkeit für Details in den Berechnungen'),
			$i18n.t('Gründliches Verständnis demonstriert'),
			$i18n.t('Andere')
		];

		DISLIKE_REASONS = [
			$i18n.t('Falsche Lösung'),
			$i18n.t('Unklare Erklärung der Schritte'),
			$i18n.t('Ineffiziente Methode verwendet'),
			$i18n.t('Mangel an logischem Denken'),
			$i18n.t('Fehler in den Berechnungen'),
			$i18n.t('Unvollständige Lösung'),
			$i18n.t('Die Anforderungen des Problems nicht erfüllt'),
			$i18n.t('Andere')
		];
	}

	let reasons = [];
	let selectedReason = null;
	let comment = '';

	$: if (message?.annotation?.rating === 1) {
		reasons = LIKE_REASONS;
	} else if (message?.annotation?.rating === -1) {
		reasons = DISLIKE_REASONS;
	}

	onMount(() => {
		selectedReason = message?.annotation?.reason ?? '';
		comment = message?.annotation?.comment ?? '';
		loadReasons();
	});

	const submitHandler = () => {
		console.log('submitHandler');

		message.annotation.reason = selectedReason;
		message.annotation.comment = comment;

		dispatch('submit');

		toast.success($i18n.t('Thanks for your feedback!'));
		show = false;
	};
</script>

<div
	class=" my-2.5 rounded-xl px-4 py-3 border dark:border-gray-850"
	id="message-feedback-{messageId}"
>
	<div class="flex justify-between items-center">
		<div class=" text-sm">{$i18n.t('Tell us more:')}</div>

		<button
			on:click={() => {
				show = false;
			}}
		>
			<svg
				xmlns="http://www.w3.org/2000/svg"
				fill="none"
				viewBox="0 0 24 24"
				stroke-width="1.5"
				stroke="currentColor"
				class="size-4"
			>
				<path stroke-linecap="round" stroke-linejoin="round" d="M6 18 18 6M6 6l12 12" />
			</svg>
		</button>
	</div>

	{#if reasons.length > 0}
		<div class="flex flex-wrap gap-2 text-sm mt-2.5">
			{#each reasons as reason}
				<button
					class="px-3.5 py-1 border dark:border-gray-850 hover:bg-gray-100 dark:hover:bg-gray-850 {selectedReason ===
					reason
						? 'bg-gray-200 dark:bg-gray-800'
						: ''} transition rounded-lg"
					on:click={() => {
						selectedReason = reason;
					}}
				>
					{reason}
				</button>
			{/each}
		</div>
	{/if}

	<div class="mt-2">
		<textarea
			bind:value={comment}
			class="w-full text-sm px-1 py-2 bg-transparent outline-none resize-none rounded-xl"
			placeholder={$i18n.t('Feel free to add specific details')}
			rows="2"
		/>
	</div>

	<div class="mt-2 flex justify-end">
		<button
			class=" bg-emerald-700 text-white text-sm font-medium rounded-lg px-3.5 py-1.5"
			on:click={() => {
				submitHandler();
			}}
		>
			{$i18n.t('Submit')}
		</button>
	</div>
</div>
