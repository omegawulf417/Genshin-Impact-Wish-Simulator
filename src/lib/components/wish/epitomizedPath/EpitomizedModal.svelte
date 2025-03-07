<script>
	import { afterUpdate } from 'svelte';
	import OverlayScrollbars from 'overlayscrollbars';
	import { t } from 'svelte-i18n';
	import {
		bannerList,
		bannerPhase,
		fatePoint,
		patchVersion,
		selectedCourse,
		showFatepointModal,
		viewportHeight,
		viewportWidth
	} from '$lib/store/stores';
	import { fade } from 'svelte/transition';
	import playSfx from '$lib/helpers/audio';
	import { localFatePoint } from '$lib/store/localstore';

	import FatepointSVG from './FatepointSVG.svelte';
	import InventoryItem from '$lib/components/inventory/InventoryItem.svelte';
	import Modal from '$lib/components/utility/ModalTpl.svelte';
	import ButtonModal from '$lib/components/utility/ButtonModal.svelte';

	$: half = $viewportWidth < 500;
	$: weaponName = $selectedCourse.name;
	$: weaponIndex = $selectedCourse.index;
	$: weapons = $bannerList.find(({ type }) => type === 'weapons')?.weapons.featured || [];

	let itemWidth;
	$: defaultItemWidth = (16.5 / 100) * $viewportHeight;
	$: if (itemWidth < 150) {
		itemWidth = 150;
	} else {
		itemWidth = defaultItemWidth;
	}

	let clientWidth;
	let showCancelConfirmation = false;

	// Target Course
	let targetActive = null;
	const select = (i) => {
		playSfx();
		targetActive = i;
	};

	const setCourse = () => {
		if (targetActive === null) return;
		const localFate = localFatePoint.init($patchVersion, $bannerPhase, targetActive + 1);
		localFate.set(0);
		const { name } = weapons[targetActive];
		selectedCourse.set({ name, index: targetActive });
		playSfx();
		handleClose();
	};

	const cancelCourse = () => {
		showCancelConfirmation = true;
		playSfx();
	};

	const confirmCancel = () => {
		const localFate = localFatePoint.init($patchVersion, $bannerPhase, targetActive);
		targetActive = null;
		selectedCourse.set({ name: null, index: null });
		fatePoint.set(0);
		localFate.remove();
		handleClose();
		showCancelConfirmation = false;
		return;
	};

	const handleClose = () => {
		showFatepointModal.set(false);
	};

	let content;
	afterUpdate(() => {
		OverlayScrollbars(content, { sizeAutoCapable: false, className: 'os-theme-light' });
	});
</script>

<Modal
	show={showCancelConfirmation}
	on:confirm={confirmCancel}
	on:cancel={() => {
		showCancelConfirmation = false;
	}}
>
	<div
		class="pop-up"
		style="display: flex; width:100%; height:100%; justify-content: center; align-items:center;"
	>
		<div>
			{$t('wish.epitomizedPath.cancelPrompt')}
			<br />
			<span style="font-size: smaller; padding: 2rem">
				{$t('wish.epitomizedPath.cancelDesc')}
			</span>
		</div>
	</div>
</Modal>

{#if $showFatepointModal}
	<section class="modal" style="height:{$viewportHeight}px" transition:fade={{ duration: 80 }}>
		<div class="modal-content" bind:clientWidth style="--modal-width: {clientWidth}px">
			<img
				src="/images/utility/fatepointbook{half ? '-half' : ''}.webp"
				alt="Fatepoint Background"
			/>
			<button
				class="close-modal"
				on:click={() => {
					handleClose();
					playSfx('close');
				}}
			>
				<i class="gi-close" />
			</button>
			<div class="container">
				{#if !half}
					<div class="description">
						<h1>{$t('wish.epitomizedPath.text')}</h1>
						<div class="content" bind:this={content}>
							{#each $t('wish.epitomizedPath.description') as desc}
								<p>
									· {@html desc}
								</p>
							{/each}
						</div>
					</div>
				{/if}
				<div class="selector" class:counter={weaponName}>
					<div class="bg">
						<FatepointSVG mode={weaponName ? 'counter' : 'bg'} />
					</div>
					<div class="top">{$t('wish.epitomizedPath.selectWeapon')}</div>
					<div class="weapon-item">
						<div class="weapon-list" style="--item-width: {itemWidth}px">
							{#if weaponName}
								<div class="weapon-content">
									<button>
										<InventoryItem
											name={weaponName}
											weaponType={weapons[weaponIndex].type}
											type="weapon"
											rarity={5}
										/>
									</button>
								</div>
							{:else}
								{#each weapons as { name, type }, i}
									<div
										class="weapon-content"
										class:active={targetActive === i}
										on:click={() => select(i)}
									>
										<button>
											<InventoryItem {name} weaponType={type} type="weapon" rarity={5} />
										</button>
									</div>
								{/each}
							{/if}
						</div>
						<div class="text">
							<div>
								{#if weaponName}
									{$t('wish.epitomizedPath.fatePoint')} : <span>{$fatePoint}</span>/2
								{:else if targetActive === null}
									{$t('wish.epitomizedPath.selectWeapon')}
								{:else}
									{@html $t('wish.epitomizedPath.chartCourseOf', {
										values: {
											target: `<span> ${$t(weapons[targetActive].name)} </span>`
										}
									})}
								{/if}
							</div>
						</div>
					</div>
					<div class="button">
						{#if weaponName}
							<ButtonModal on:click={cancelCourse} type="cancel">
								{$t('wish.epitomizedPath.cancelCourse')}
							</ButtonModal>
						{:else}
							<ButtonModal on:click={setCourse}>
								{$t('wish.epitomizedPath.chartCourse')}
							</ButtonModal>
						{/if}
					</div>
				</div>
			</div>
		</div>
	</section>
{/if}

<style>
	.modal {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		background-color: rgba(0, 0, 0, 0.7);
		z-index: 10;
		display: flex;
		justify-content: center;
		align-items: center;
		backdrop-filter: blur(2px);
	}

	.modal-content {
		width: 1000px;
		max-width: 90%;
		min-width: 250px;
		text-align: center;
		position: relative;
		border-radius: 1.2rem;
		overflow: hidden;
	}

	:global(.mobile) .modal-content {
		max-width: 140vh;
		border-radius: 0.8rem;
	}

	.modal-content img {
		position: relative;
		width: 100%;
	}

	.close-modal {
		position: absolute;
		top: 1.5rem;
		right: -0.2rem;
		z-index: +10;
	}
	.gi-close {
		font-size: 1.3rem;
		background-color: transparent;
		color: #383b40;
	}
	.container {
		width: 100%;
		height: 100%;
		display: flex;
		position: absolute;
		top: 0;
		left: 0;
	}

	.container h1 {
		text-align: left;
		padding-left: 10%;
		padding-top: 5%;
		font-size: calc(3 / 100 * var(--modal-width));
		color: #7c613f;
	}

	.container > div {
		flex-basis: 50%;
		flex-grow: 1;
		padding: 3% 7%;
	}

	.description {
		padding-right: 5.5% !important;
		font-size: calc(2 / 100 * var(--modal-width));
	}

	.container .content {
		width: 100%;
		height: 82%;
		overflow: hidden;
		margin: 8% 0 0;
		text-align: left;
		color: #aa8e77;
	}

	.selector {
		display: flex;
		flex-direction: column;
		height: 100%;
		color: #383b40;
	}
	.selector,
	.selector > div {
		position: relative;
		padding: 5%;
	}
	.selector .bg {
		position: absolute;
		width: 90%;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
	}
	.counter.selector .bg {
		width: 110%;
		top: 48%;
	}

	.top {
		font-size: calc(3 / 100 * var(--modal-width));
		white-space: nowrap;
	}

	.weapon-item {
		display: flex;
		flex-direction: column;
		height: 100%;
		border: solid #dcd8cd;
		border-width: 3px 0;
		font-size: x-large;
		padding-left: 0 !important;
		padding-right: 0 !important;
	}

	.counter .weapon-item {
		border: 0;
	}

	.weapon-list {
		height: 100%;
		width: 100%;
		padding: 0 10%;
		background-color: #dcd8cd;
		display: flex;
		justify-content: center;
		align-items: center;
		overflow: hidden;
		text-align: center;
		color: #3a4156;
		line-height: 1.2rem;
	}
	.counter .weapon-list {
		background-color: transparent;
	}

	.weapon-content {
		display: inline-block;
		padding: 5%;
		width: 50%;
	}

	.weapon-content button {
		font-size: small;
		aspect-ratio: 8.75 / 10;
		position: relative;
		vertical-align: middle;
		width: 100%;
	}
	:global(.mobile) .weapon-content button {
		font-size: xx-small;
	}

	.weapon-content.active button::after,
	.weapon-content.active button::before {
		position: absolute;
		right: 0;
		top: 0;
	}

	.weapon-content.active button::after {
		display: block;
		content: '';
		width: 100%;
		height: calc(100% - 0.4rem);
		border: solid #bed634;
		border-width: 0.2rem 0;
		border-radius: 0.3rem;
	}
	.weapon-content.active button::before {
		content: '✔';
		font-size: 1.2rem;
		color: #759a28;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #bed634;
		width: 20%;
		height: 20%;
		z-index: +1;
		border-top-right-radius: 0.5em;
	}

	.text {
		margin-top: auto;
		height: 40%;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	span,
	p :global(span),
	.text :global(span) {
		color: #f0b164;
	}

	.counter .text {
		height: unset;
		margin-bottom: -1rem;
	}

	button i {
		width: 2rem;
		height: 2rem;
		background-color: #353533;
		border-radius: 100%;
		display: inline-flex;
		justify-content: center;
		align-items: center;
		font-size: 1rem;
		margin-right: 2rem;
	}

	@media screen and (max-width: 800px) and (min-width: 500px) {
		.weapon-item {
			font-size: medium;
		}
	}

	:global(.mobile) .text {
		height: 30%;
	}
	:global(.mobile) .counter .text {
		height: unset;
		margin-bottom: -1rem;
	}
	:global(.mobile) .weapon-item {
		font-size: small;
	}
</style>
