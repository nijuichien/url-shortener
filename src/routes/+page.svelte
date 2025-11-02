<script lang="ts">
	let url = '';
	let shortUrl = '';
	let showPopup = false;

	async function shortenUrl() {
		if (!url) return;
		
		// 模擬生成短網址 (實際應用需要後端 API)
		const randomId = Math.random().toString(36).substring(2, 8);
		shortUrl = `${window.location.origin}/${randomId}`;
		showPopup = true;
	}

	function closePopup() {
		showPopup = false;
	}

	function copyToClipboard() {
		navigator.clipboard.writeText(shortUrl);
		alert('短網址已複製到剪貼簿！');
	}
</script>

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
	<div class="popup-overlay" on:click={closePopup}>
		<div class="popup" on:click|stopPropagation>
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
</style>
