<script>
	import { onMount } from 'svelte';
	import { fly } from 'svelte/transition';
	import { t } from 'svelte-i18n';
	import OverlayScrollbars from 'overlayscrollbars';
	import { data as charsDB } from '$lib/data/characters.json';
	import positionToStyle from '$lib/helpers/cssPosition';

	export let data = {};

	const getBannerName = (banner) => {
		const split = banner.split('-');
		return split.slice(0, -1).join('-');
	};

	let character, name, textOffset;
	$: bannerName = $t(`wish.banner.name.${getBannerName(name)}`, { default: 'UnReleased Banner' });
	$: ({ character, name, textOffset } = data);
	$: vision = charsDB[0].list.find(({ name }) => name === character).vision;
	$: featuredC = `--text-width: calc(${textOffset?.w || 30} / 100 * var(--content-width));`;

	const highlightBannerName = (bannerName) => {
		const splited = bannerName.split(' ');
		return `<span class="${vision}-flat">${splited[0]}</span> ${splited.slice(1).join(' ')}`;
	};

	let bannerInfo;
	onMount(() => {
		OverlayScrollbars(bannerInfo, {
			sizeAutoCapable: false,
			className: 'os-theme-light',
			scrollbars: { visibility: 'hidden' }
		});
	});
</script>

<div class="frame-content">
	<div class="top bg-{vision}">
		{$t('wish.banner.events')}
	</div>
	<h1 class="card-stroke" in:fly={{ x: 15, duration: 700 }}>
		{@html highlightBannerName(bannerName)}
	</h1>

	<div class="info" bind:this={bannerInfo} in:fly={{ x: 15, duration: 700 }}>
		<div class="content">
			<div class="set card-stroke">
				{$t('wish.banner.probIncreased')}
			</div>
			<div class="desc bg-{vision}" style="opacity: 90%;">
				<div class="icon">
					<i class="gi-primo-star" />
				</div>
				<div class="text">
					{$t('wish.banner.wishDescription')}
				</div>
			</div>
			<div class="note card-stroke">
				{$t('wish.banner.eventNote')}
				{$t('wish.banner.viewDetails')}
			</div>
		</div>
	</div>

	<div
		class="character"
		style="{featuredC}{positionToStyle(textOffset)}"
		in:fly={{ x: 10, duration: 700 }}
	>
		<div class="char-name">
			<span>
				{$t(`${character}.name`)}
			</span>
			<span class="up">{$t('wish.banner.up')}</span>
		</div>
		<div class="char-title">
			{$t(`${character}.title`)}
		</div>
	</div>
</div>

<style>
	.frame-content {
		width: 100%;
		height: 100%;
		position: relative;
		color: #565654;
		display: block;
		font-size: calc(1.8 / 100 * var(--content-width));
		line-height: 130%;
	}

	h1 :global(span) {
		display: block;
	}

	h1,
	.frame-content > div {
		text-align: left;
		position: absolute;
	}
	h1 {
		bottom: 67%;
		left: 0;
		margin: 0 4%;
		line-height: 125%;
		font-size: calc(4.5 / 100 * var(--content-width));
	}

	.top {
		color: #fff;
		padding: 0.3% 1.4%;
		border-bottom-left-radius: 2rem;
		border-top-left-radius: 2rem;
		border-bottom-right-radius: 4rem;
		top: 0;
		left: 0;
		transform: translate(-3%, -15%);
	}

	.info {
		left: 0;
		top: 40%;
		width: 40%;
		height: 45%;
		display: block;
		padding-left: 4%;
	}

	.content {
		position: relative;
	}

	.info .content::after {
		content: '';
		display: block;
		width: calc(0.55 / 100 * var(--content-width));
		height: 100%;
		background-color: #565654;
		position: absolute;
		left: calc(-3.045 / 100 * var(--content-width));
		top: 0;
	}

	.set {
		font-size: calc(2.4 / 100 * var(--content-width));
	}

	.desc {
		left: 7.5%;
		top: 49.7%;
		color: #fff;
		min-height: calc(9 / 100 * var(--content-height));
		display: flex;
		align-items: center;
		margin: calc(0.7 / 100 * var(--content-width)) 0;
	}

	.icon {
		display: flex;
		justify-content: center;
		align-items: center;
		padding: calc(1 / 100 * var(--content-width));
		font-size: calc(1.1 / 100 * var(--content-width));
	}

	.desc .text {
		width: calc(32.5 / 100 * var(--content-width));
		padding: calc(0.3 / 100 * var(--content-width));
	}

	.character {
		left: 54%;
		bottom: 8%;
		width: calc(27 / 100 * var(--content-width));
	}

	.character .char-name {
		color: #fff;
		display: inline-block;
		-webkit-text-stroke: 0.02rem #565654;
		text-shadow: 0 0 0.15rem #d2c69c;
		line-height: 100%;
		position: relative;
		font-size: calc(11 / 100 * var(--text-width));
	}

	.char-name span {
		filter: drop-shadow(0 0.3rem 0.5rem #000);
	}

	.char-name .up {
		color: #fff664;
		-webkit-text-stroke: 0.05rem #e7a12e;
		font-size: calc(2 / 100 * var(--content-width));
		filter: drop-shadow(0 0.3rem 0.5rem #fff);
		position: absolute;
		top: 0;
		right: 0;
		transform: translateX(100%) translateY(-50%);
		text-transform: uppercase;
		text-shadow: 0 0 0.4rem #f79c09;
	}

	.character .char-title {
		color: #cfbc99;
		background-color: #39425d;
		margin-top: calc(3.5 / 100 * var(--content-width));
		padding: 1% 2%;
		white-space: nowrap;
		width: fit-content;
	}
</style>
