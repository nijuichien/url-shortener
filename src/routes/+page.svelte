<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import { base } from '$app/paths';

	const BACKEND_API = 'https://dwvepkgvntnzgiubnapy.supabase.co/functions/v1/get_url';
	const INSERT_API = 'https://dwvepkgvntnzgiubnapy.supabase.co/functions/v1/insert-url';

	let url = '';
	let shortUrl = '';
	let showPopup = false;
	
	// 跳轉邏輯
	let loading = false;
	let errorMessage = '';
	let countdown = 3;
	let targetUrl = '';

	onMount(async () => {
		if (!browser) return;

		// 檢查是否有 shortCode query parameter
		const params = new URLSearchParams(window.location.search);
		const shortCode = params.get('shortCode');

		if (shortCode) {
			loading = true;
			try {
				// 呼叫 Supabase 後端 API
				const backendResponse = await fetch(BACKEND_API, {
					method: 'POST',
					headers: { 'Content-Type': 'application/json' },
					body: JSON.stringify({ shortCode })
				});

				if (!backendResponse.ok) {
					if (backendResponse.status === 404) throw new Error('找不到此短網址');
					throw new Error('無法取得短網址');
				}

				const { url: targetUrl_ } = await backendResponse.json();
				targetUrl = targetUrl_;

				// 倒數 3 秒再跳轉
				const timer = setInterval(() => {
					countdown -= 1;
					if (countdown <= 0) {
						clearInterval(timer);
						window.location.href = targetUrl_;
					}
				}, 1000);

			} catch (err) {
				loading = false;
				errorMessage = err instanceof Error ? err.message : '發生錯誤';
			}
		}
	});

	async function shortenUrl() {
		if (!url) return;
		
		// 驗證網址格式
		try {
			new URL(url);
		} catch {
			alert('請輸入有效的網址');
			return;
		}

		// 生成短代碼
		const randomId = Math.random().toString(36).substring(2, 8);
		
		try {
			// 呼叫 Supabase 插入 API
			const response = await fetch(INSERT_API, {
				method: 'POST',
				headers: { 'Content-Type': 'application/json' },
				body: JSON.stringify({ 
					originalUrl: url, 
					shortCode: randomId 
				})
			});

			if (!response.ok) {
				throw new Error('無法創建短網址，請稍後再試');
			}

			shortUrl = `${window.location.origin}${base}/?shortCode=${randomId}`;
			showPopup = true;
		} catch (error) {
			alert(error instanceof Error ? error.message : '創建短網址失敗');
		}
	}

	function closePopup() {
		showPopup = false;
	}

	function copyToClipboard() {
		navigator.clipboard.writeText(shortUrl);
		alert('短網址已複製到剪貼簿！');
	}
</script>

{#if loading}
	<div class="redirect-container">
		<div class="loader"></div>
		<p>即將跳轉至：<br><span class="target-url">{targetUrl}</span></p>
		<p class="countdown">剩餘 {countdown} 秒...</p>

		<!-- 廣告區 -->
		<div class="ad-container">
			<p>廣告區域</p>
		</div>
	</div>
{:else if errorMessage}
	<div class="error-container">
		<div class="error-box">
			<h1>❌ 錯誤</h1>
			<p>{errorMessage}</p>
			<a href="{base}/" class="home-btn">返回首頁</a>
		</div>
	</div>
{:else}
	<div class="container">
		<h1>短網址產生器</h1>
		<div class="input-group">
			<input 
				type="url" 
				bind:value={url} 
				placeholder="輸入您的網址"
				class="url-input"
			/>
			<button on:click={shortenUrl} class="shorten-btn">
				縮短網址
			</button>
		</div>
	</div>

	{#if showPopup}
		<div class="popup-overlay" role="dialog" aria-modal="true" on:click={closePopup} on:keydown={(e) => e.key === 'Escape' && closePopup()}>
			<div class="popup" role="document" on:click|stopPropagation on:keydown={(e) => e.key === 'Escape' && closePopup()}>
				<h2>您的短網址</h2>
				<div class="short-url-display">
					{shortUrl}
				</div>
				<div class="popup-buttons">
					<button on:click={copyToClipboard} class="copy-btn">
						複製網址
					</button>
					<button on:click={closePopup} class="close-btn">
						關閉
					</button>
				</div>
			</div>
		</div>
	{/if}
{/if}

<style>
	.container {
		max-width: 600px;
		margin: 100px auto;
		padding: 20px;
		text-align: center;
	}

	h1 {
		font-size: 2.5rem;
		margin-bottom: 40px;
		color: #333;
	}

	.input-group {
		display: flex;
		gap: 10px;
		margin-bottom: 20px;
	}

	.url-input {
		flex: 1;
		padding: 15px;
		font-size: 16px;
		border: 2px solid #ddd;
		border-radius: 8px;
		outline: none;
		transition: border-color 0.3s;
	}

	.url-input:focus {
		border-color: #4CAF50;
	}

	.shorten-btn {
		padding: 15px 30px;
		font-size: 16px;
		background-color: #4CAF50;
		color: white;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: background-color 0.3s;
		font-weight: bold;
	}

	.shorten-btn:hover {
		background-color: #45a049;
	}

	.popup-overlay {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: rgba(0, 0, 0, 0.5);
		display: flex;
		justify-content: center;
		align-items: center;
		z-index: 1000;
	}

	.popup {
		background: white;
		padding: 30px;
		border-radius: 12px;
		max-width: 500px;
		width: 90%;
		box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
	}

	.popup h2 {
		margin-top: 0;
		color: #333;
	}

	.short-url-display {
		background-color: #f5f5f5;
		padding: 15px;
		border-radius: 8px;
		margin: 20px 0;
		word-break: break-all;
		font-family: monospace;
		font-size: 16px;
		color: #4CAF50;
	}

	.popup-buttons {
		display: flex;
		gap: 10px;
		justify-content: center;
	}

	.copy-btn, .close-btn {
		padding: 10px 20px;
		font-size: 16px;
		border: none;
		border-radius: 8px;
		cursor: pointer;
		transition: background-color 0.3s;
		font-weight: bold;
	}

	.copy-btn {
		background-color: #2196F3;
		color: white;
	}

	.copy-btn:hover {
		background-color: #0b7dda;
	}

	.close-btn {
		background-color: #f44336;
		color: white;
	}

	.close-btn:hover {
		background-color: #da190b;
	}

	/* 跳轉頁面樣式 */
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

	@keyframes spin { 
		0% { transform: rotate(0deg); } 
		100% { transform: rotate(360deg); } 
	}

	.target-url {
		color: #4CAF50;
		font-weight: bold;
		word-break: break-all;
		font-size: 0.9em;
	}

	.countdown {
		margin-top: 20px;
		font-size: 1.2em;
		font-weight: bold;
		color: #2196F3;
	}

	.error-container {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 50vh;
	}

	.error-box {
		border: 2px solid #f00;
		padding: 30px;
		border-radius: 8px;
		background: white;
		box-shadow: 0 2px 10px rgba(0,0,0,0.1);
	}

	.error-box h1 {
		margin-top: 0;
		color: #f00;
	}

	.home-btn {
		display: inline-block;
		margin-top: 20px;
		padding: 10px 20px;
		background: #333;
		color: #fff;
		text-decoration: none;
		border-radius: 5px;
		transition: background 0.3s;
	}

	.home-btn:hover {
		background: #555;
	}

	.ad-container {
		margin-top: 30px;
		padding: 20px;
		border: 2px dashed #ccc;
		background: #f9f9f9;
		border-radius: 8px;
		max-width: 600px;
		margin-left: auto;
		margin-right: auto;
	}
</style>
