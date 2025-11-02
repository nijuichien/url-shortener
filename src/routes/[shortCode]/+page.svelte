<script lang="ts">
	import { onMount } from 'svelte';
	import { page } from '$app/stores';
	import { browser } from '$app/environment';

	const BACKEND_API = 'https://dwvepkgvntnzgiubnapy.supabase.co/functions/v1/get_url';

	let loading = true;
	let errorMessage = '';
	let countdown = 3; // 倒數秒數

	onMount(async () => {
		if (!browser) return;

		// 取得 URL path 參數
		const shortCode = $page.params.shortCode;

		try {
			const backendResponse = await fetch(BACKEND_API, {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify({ shortCode })
			});

			if (!backendResponse.ok) {
				if (backendResponse.status === 404) throw new Error('找不到此短網址');
				throw new Error('無法取得短網址');
			}

			const { url } = await backendResponse.json();

			// 倒數 3 秒再跳轉
			const timer = setInterval(() => {
				countdown -= 1;
				if (countdown <= 0) {
					clearInterval(timer);
					window.location.href = url;
				}
			}, 1000);

		} catch (err) {
			loading = false;
			errorMessage = err instanceof Error ? err.message : '發生錯誤';
		}
	});
</script>

{#if browser}
	{#if loading}
		<div class="redirect-container">
			<div class="loader"></div>
			<p>即將跳轉，剩餘 {countdown} 秒...</p>

			<!-- 廣告區 -->
			<div class="ad-container">
				<!-- 可插入廣告 script 或 iframe -->
				<p>廣告區域</p>
			</div>
		</div>
	{:else if errorMessage}
		<div class="error-container">
			<div class="error-box">
				<h1>❌ 錯誤</h1>
				<p>{errorMessage}</p>
				<a href="/" class="home-btn">返回首頁</a>
			</div>
		</div>
	{/if}
{:else}
	<div class="redirect-container">
		<div class="loader"></div>
		<p>載入中...</p>
	</div>
{/if}

<style>
	.redirect-container {
		text-align: center;
		margin-top: 50px;
	}
	.loader {
		border: 5px solid #f3f3f3;
		border-top: 5px solid #333;
		border-radius: 50%;
		width: 50px;
		height: 50px;
		animation: spin 1s linear infinite;
		margin: auto;
	}
	@keyframes spin { 0% { transform: rotate(0deg); } 100% { transform: rotate(360deg); } }

	.error-box {
		border: 1px solid #f00;
		padding: 20px;
		display: inline-block;
		margin-top: 50px;
	}
	.home-btn {
		display: inline-block;
		margin-top: 10px;
		padding: 5px 10px;
		background: #333;
		color: #fff;
		text-decoration: none;
		border-radius: 3px;
	}
	.ad-container {
		margin-top: 20px;
		padding: 10px;
		border: 1px dashed #ccc;
		background: #f9f9f9;
	}
</style>
