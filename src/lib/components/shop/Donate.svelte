<script>
	import { onMount } from 'svelte';
	import playSfx from '$lib/helpers/audio';
	import { supporterList } from '$lib/helpers/donation';
	import { copy } from '$lib/helpers/nameText';
	import Modal from '../utility/ModalTpl.svelte';
	import ColumnParent from './parts/_column-parent.svelte';
	import Column from './parts/_column.svelte';

	let showCryptoModal = false;
	let showToast = false;

	const copyHandle = (text) => {
		playSfx();
		copy(text);
		showToast = true;
		const t = setTimeout(() => {
			showToast = false;
			clearTimeout(t);
		}, 2000);
	};

	let listOfSupporters = [];
	onMount(async () => {
		listOfSupporters = await supporterList();
	});
</script>

<!-- Crypto Donate -->
<Modal
	button="confirm"
	show={showCryptoModal}
	title="Support With Crypto"
	on:confirm={() => {
		showCryptoModal = false;
	}}
>
	<div class="modal-donate">
		<div class="pop-item">
			<div class="icon">
				<img src="/images/utility/donate-ethereum.png" alt="Ethereum" />
			</div>
			<div class="address">
				<span> Ethereum ( erc20 ) </span>
				<input type="text" value="0x4320025BAD621c03b906A84c531B10480A465184" disabled />
			</div>
			<div class="copy">
				<button on:click={() => copyHandle('0x4320025BAD621c03b906A84c531B10480A465184')}
					><i class="gi-copy" /></button
				>
			</div>
		</div>

		<div class="pop-item">
			<div class="icon">
				<img src="/images/utility/donate-bnb.png" alt="Binance Coin" />
			</div>
			<div class="address">
				<span> Binance Coin ( bep20 )</span>
				<input type="text" value="0x4320025BAD621c03b906A84c531B10480A465184" disabled />
			</div>
			<div class="copy">
				<button on:click={() => copyHandle('0x4320025BAD621c03b906A84c531B10480A465184')}>
					<i class="gi-copy" />
				</button>
			</div>
		</div>

		<div class="pop-item">
			<div class="icon">
				<img src="/images/utility/donate-solana.png" alt="Solana" />
			</div>
			<div class="address">
				<span> Solana </span>
				<input type="text" value="4nFhLoPqpx71xPqgN2zhvoWtmgogzoDkEBzNKqjnpm2a" disabled />
			</div>
			<div class="copy">
				<button on:click={() => copyHandle('4nFhLoPqpx71xPqgN2zhvoWtmgogzoDkEBzNKqjnpm2a')}>
					<i class="gi-copy" />
				</button>
			</div>
		</div>

		{#if showToast}
			<div class="toast">Address Copied</div>
		{/if}
	</div>
</Modal>

<!-- Crypto Donate -->
<div class="container">
	<ColumnParent>
		<Column>
			<a class="content kofi" href="https://ko-fi.com/mantan21" target="_blank">
				<div
					style="display: flex;justify-content: center; align-items: center; width: 100%; height: 100%"
				>
					<div class="donate-icon">
						<img src="/images/utility/donate-kofi.png" alt="Ko-fi Icon" />
						<img src="/images/utility/paypal.png" alt="paypal" />
					</div>
				</div>
				<span> Support me on Ko-fi </span>
			</a>
		</Column>

		<!-- Donaate By Saweria -->
		<Column style="padding: 0.4rem;">
			<a class="content Saweria" href="https://saweria.co/AguzzTN54" target="_blank">
				<div
					style="display: flex;justify-content: center; align-items: center; width: 100%; height: 100%"
				>
					<div class="donate-icon">
						{#each ['ovo', 'dana', 'linkaja'] as im}
							<img src="/images/utility/donate-{im}.png" alt="{im} icon" />
						{/each}
					</div>
				</div>
				<span> Support me on Saweria </span>
			</a>
		</Column>

		<!-- Donate Sociabuzz -->
		<Column style="padding: 0.4rem;">
			<a class="content sociabuzz" href="https://sociabuzz.com/mantan21/posts" target="_blank">
				<div
					style="display: flex;justify-content: center; align-items: center; width: 100%; height: 100%"
				>
					<div class="donate-icon">
						<img src="/images/utility/sociabuzz.png" alt="icon" />
						<span style="font-size: 80%; color:darkblue">Global & Local Payment</span>
					</div>
				</div>
				<span> Support me on SociaBuzz </span>
			</a>
		</Column>

		<!-- Donate By Crypto -->
		<Column style="padding: 0.4rem;">
			<button
				class="content crypto"
				on:click={() => {
					showCryptoModal = true;
				}}
			>
				<div
					style="display: flex;justify-content: center; align-items: center; width: 100%; height: 100%"
				>
					<div class="donate-icon">
						{#each ['btc', 'ethereum', 'bnb', 'solana'] as im}
							<img src="/images/utility/donate-{im}.png" alt="{im} icon" />
						{/each}
					</div>
				</div>
				<span> Support me with Crypto </span>
			</button>
		</Column>
	</ColumnParent>
	<div class="recent">
		{#each listOfSupporters as { name, message, amount, date, platform }}
			<div class="donation-item {platform}">
				<div class="supporter">
					<div class="info">
						<div class="name">New support from <span> {name} </span></div>
						<span class="message">{message ? `"${message}"` : ''}</span>
						<span class="platform">✧ &nbsp; via {platform}</span>
						<span class="time"> ✧ &nbsp; {date}</span>
					</div>
					<div class="amount">
						<span>{amount}</span>
					</div>
				</div>
			</div>
		{/each}
	</div>
</div>

<style>
	/* Donate */
	.toast {
		position: fixed;
		top: 50%;
		left: 50%;
		transform: translate(-50%, -50%);
		display: inline-block;
		padding: 0.5rem 1rem;
		border-radius: 0.5rem;
		background-color: rgba(173, 128, 65, 0.8);
		color: #fff;
		font-size: 0.75rem;
	}

	.modal-donate .pop-item {
		display: flex;
		align-items: center;
		width: 100%;
	}

	.modal-donate .icon,
	.modal-donate .copy {
		display: flex;
		height: 100%;
		justify-content: center;
		align-items: center;
		margin: 0.2rem;
	}
	.modal-donate img {
		height: 3rem;
		margin: 0;
	}
	.address {
		padding: 0 0 0 1rem;
		display: flex;
		flex-direction: column;
		text-align: left;
		width: 100%;
	}
	.address span {
		font-size: 0.8rem;
	}

	.modal-donate button {
		background-color: #383b40;
		color: #fff;
		transition: all 0.2s;
		border-radius: 100%;
		width: 3rem;
		height: 3rem;
		display: flex;
		justify-content: center;
		align-items: center;
		font-size: 1rem;
	}
	.modal-donate button:hover {
		background-color: #ccc;
		color: #000;
	}

	.content {
		background-color: rgba(255, 255, 255, 0.8);
		height: 100%;
		width: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		border-radius: 1rem;
		padding: 1rem;
		text-align: center;
		transition: transform 0.1s;
	}

	.content:active {
		transform: scale(0.95);
	}

	.donate-icon {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		align-items: center;
	}
	img {
		height: 1.5rem;
		margin: 0.2rem 0.5rem;
	}
	span {
		padding: 0.5rem;
	}

	.container {
		display: flex;
		flex-direction: column;
		width: 100%;
		height: calc(100vh - 130px);
	}
	:global(.mobile) .container {
		height: calc(100vh - 60px);
	}

	.recent {
		display: flex;
		flex-wrap: wrap;
		overflow-y: auto;
		max-height: 60%;
		justify-content: space-between;
		align-items: flex-start;
		font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
	}

	.donation-item {
		width: 50%;
		margin: 0;
		padding: 0.5%;
	}

	.donation-item .supporter {
		background-color: rgba(255, 255, 255, 0.8);
		width: 100%;
		height: 100px;
		padding: 2%;
		border-radius: 0.5rem;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	@media screen and (max-width: 640px) {
		.recent {
			max-height: 50%;
		}

		.donation-item {
			width: 100%;
		}
		.supporter {
			height: unset !important;
		}
		.info {
			overflow-y: unset !important;
		}
	}

	:global(.mobile) .recent {
		max-height: 50%;
	}
	:global(.mobile) .supporter {
		height: 5rem;
	}
	:global(.mobile) .info {
		overflow-y: auto;
	}

	.info {
		font-size: smaller;
		width: 100%;
		height: 100%;
		overflow-y: auto;
	}

	.info::-webkit-scrollbar {
		display: none;
	}

	.info .name {
		margin-left: 0.5rem;
	}
	.info .name span {
		font-family: 'Genshin Impact';
		font-size: larger;
	}

	.message {
		display: block;
		font-weight: 600;
		padding: 0.4rem 1rem;
	}

	.ko-fi .platform {
		color: #127399;
		margin-right: 0.5rem;
	}
	.sociabuzz .platform {
		color: #4f8d28;
		margin-right: 0.5rem;
	}

	.saweria .platform {
		color: rgb(213, 142, 18);
		margin-right: 0.5rem;
	}

	.time {
		color: #575859;
	}

	.amount span {
		width: fit-content;
		font-size: 0.7rem;
		white-space: nowrap;
		padding: 0.4rem 0.6rem;
		border-radius: 1rem;
		color: #fff;
		font-family: 'Genshin Impact';
	}

	.donation-item.ko-fi .amount span {
		background-color: #24ade1;
	}
	.donation-item.saweria .amount span {
		background-color: #e2a12d;
	}

	.donation-item.sociabuzz .amount span {
		background-image: linear-gradient(45deg, #3fa9f5 30%, #78c845);
	}
</style>
