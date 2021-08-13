<script lang="ts">
	// @ts-nocheck
	import { autoresize } from 'svelte-textarea-autoresize';
	import axios from 'axios';
	import { RingLoader } from 'svelte-loading-spinners';

	let text: string = '';
	let words: Array<string> = [];
	let loading = false;

	const parseText = async () => {
		if (text.length <= 2) return;
		loading = true;
		try {
			const a = await axios.post(
				'https://www.paddlepaddle.org.cn/wordtag',
				JSON.stringify({ text })
			);
			words = await a.data['results'][0]['items'];
		} finally {
			loading = false;
		}
	};

	const parseTerm = (term: string) => {
		term = term || '';

		term = term.replace('eb', '实体');
		term = term.replace('cb', '概念');

		return term;
	};

	$: loading;
</script>

<br />
<div class="container">
	<div>
		<button class="waves-effect waves-light btn blue" on:click={parseText}>进行智能解析！</button>
	</div>
	<br />

	<textarea placeholder="请输入案件文本" bind:value={text} use:autoresize />

	<br /><br />
	<div>解析结果：</div>

	{#if !loading}
		<div class="wordlist">
			{#each words as word}
				<div class="word">
					<div class="item">{word.item}</div>
					<div class="label">{word.wordtag_label}</div>
					<div class="termid">{parseTerm(word?.termid)}</div>
				</div>
			{:else}
				<div class="logoimg">
					<img width="100%" alt="logo" src="/favicon.png" />
				</div>
			{/each}
		</div>
	{:else}
		<div class="loading"><RingLoader size="60" color="#006eff" unit="px" duration="1s" /></div>
	{/if}
</div>

<style>
	@font-face {
		font-family: 'oppo';
		src: url('/OPPOSans-M.ttf');
		font-weight: normal;
		font-style: normal;
	}

	textarea {
		resize: none;

		box-sizing: border-box;
		margin: 0;
		font-variant: tabular-nums;
		list-style: none;
		font-feature-settings: 'tnum', 'tnum';
		position: relative;
		display: inline-block;
		width: 100%;
		min-width: 0;
		padding: 4px 11px;
		color: rgba(0, 0, 0, 0.85);
		font-size: 14px;
		background-color: #fff;
		background-image: none;
		border: 1px solid #d9d9d9;
		border-radius: 2px;
		transition: all 0.3s;
		border: 1px solid rgba(0, 0, 0, 0.1);
		border-radius: 16px;
		color: #666;
		font-size: 14px;
		line-height: 35px;
		font-weight: 400;
		outline: none;
		text-align: left;
	}

	textarea:focus {
		border-color: rgba(82, 168, 236, 0.8);
		outline: 0;
		outline: thin dotted \9;
		-webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
		-moz-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
		box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075), 0 0 8px rgba(82, 168, 236, 0.6);
	}

	.word {
		display: block;
		margin-top: 24px;
		background: rgba(140, 161, 219, 0.4);
		border-radius: 4px;
		padding: 8px 24px;
		text-align: center;
		margin-right: 11px;
		box-shadow: 0px 1px 2px rgba(47, 100, 141, 0.4);
		border-radius: 5px;
		display: flex;
		flex-direction: column;
		line-height: 200%;
		transition: all 0.3s;
	}

	.word:hover {
		box-shadow: 0 8px 17px 2px rgb(47 100 141 / 14%), 0 3px 14px 2px rgb(47 100 141 / 12%),
			0 5px 5px -3px rgb(47 100 141 / 20%);
	}

	.wordlist {
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
	}

	.container {
		font-family: oppo;
	}

	.item {
		font-weight: 800;
		font-size: 16px;
	}

	.label {
		color: rgb(0, 0, 0, 0.7);
	}

	.termid {
		color: rgb(0, 81, 255);
	}

	.loading {
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.logoimg {
		width: 40%;
	}
</style>
