<script lang="ts">
	import { page } from '$app/stores';
</script>

<div class="error-container">
	<div class="error-box">
		<div class="error-code">404</div>
		<h1>找不到此短網址</h1>
		<p>您輸入的短網址不存在或已過期</p>
		
		{#if $page.url.pathname !== '/'}
			<div class="url-display">
				<strong>請求的網址：</strong>{$page.url.pathname}
			</div>
		{/if}

		<div class="suggestions">
			<h3>可能的原因：</h3>
			<ul>
				<li>短網址輸入錯誤</li>
				<li>短網址已過期或被刪除</li>
				<li>連結已失效</li>
			</ul>
		</div>

		<a href="/" class="home-btn">
			← 返回首頁
		</a>
	</div>
</div>

<style>
	.error-container {
		display: flex;
		justify-content: center;
		align-items: center;
		min-height: 100vh;
		background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
		padding: 20px;
	}

	.error-box {
		background: white;
		padding: 50px 40px;
		border-radius: 16px;
		max-width: 600px;
		box-shadow: 0 10px 40px rgba(0, 0, 0, 0.3);
		text-align: center;
	}

	.error-code {
		font-size: 120px;
		font-weight: bold;
		color: #667eea;
		line-height: 1;
		margin-bottom: 20px;
		text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
	}

	h1 {
		font-size: 2rem;
		color: #333;
		margin-bottom: 15px;
	}

	p {
		color: #666;
		font-size: 18px;
		margin-bottom: 30px;
	}

	.url-display {
		background-color: #f5f5f5;
		padding: 15px;
		border-radius: 8px;
		margin: 25px 0;
		word-break: break-all;
		font-family: monospace;
		font-size: 14px;
		color: #666;
		text-align: left;
	}

	.url-display strong {
		color: #333;
		display: block;
		margin-bottom: 8px;
	}

	.suggestions {
		background-color: #fff3cd;
		border-left: 4px solid #ffc107;
		padding: 20px;
		margin: 30px 0;
		text-align: left;
		border-radius: 4px;
	}

	.suggestions h3 {
		margin-top: 0;
		margin-bottom: 15px;
		color: #856404;
		font-size: 16px;
	}

	.suggestions ul {
		margin: 0;
		padding-left: 20px;
		color: #856404;
	}

	.suggestions li {
		margin-bottom: 8px;
	}

	.home-btn {
		display: inline-block;
		padding: 15px 40px;
		background-color: #667eea;
		color: white;
		text-decoration: none;
		border-radius: 8px;
		font-weight: bold;
		font-size: 16px;
		transition: all 0.3s;
		margin-top: 20px;
	}

	.home-btn:hover {
		background-color: #5568d3;
		transform: translateY(-2px);
		box-shadow: 0 4px 12px rgba(102, 126, 234, 0.4);
	}

	@media (max-width: 600px) {
		.error-code {
			font-size: 80px;
		}

		h1 {
			font-size: 1.5rem;
		}

		.error-box {
			padding: 30px 20px;
		}
	}
</style>
