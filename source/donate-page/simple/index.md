---
title: donate-page
date: 2017-04-13 21:33:23
comments: false
layout: false
---
{% raw %}
<!DOCTYPE html>
<html lang="zh">
<head>
	<meta charset="UTF-8">
	<title>Donate-Page</title>
	<meta name="viewport" content="width=device-width, initial-scale=1, minimum-scale=1.0, maximum-scale=1.0, minimal-ui" /><!--禁止缩放-->
	<link rel="stylesheet" href="style.css">
	<script src="https://ajax.aspnetcdn.com/ajax/jQuery/jquery-2.0.3.min.js"></script>
	<script src="https://cdnjs.cloudflare.com/ajax/libs/clipboard.js/1.5.10/clipboard.min.js"></script>
	<!-- clipboard.js 是 JS 复制文本的库 -->
	<script src="script.js"></script>
</head>
<body>

	<div id="DonateText" class="tr3">Donate</div>
	<ul id="donateBox" class="list pos-f tr3">
		<li id="PayPal"><a href="#" target="_blank">PayPal</a></li>
		<li id="BTC" data-footnote="Copy addres and show QRCod"><button id="BTCBn"  data-clipboard-target="#btc-key" alt="Copy to clipboard">Bitcoin</button></li>
		<li id="AliPay">AliPay</li>
		<li id="WeChat">WeChat</li>
	</ul>
	<div id="QRBox" class="pos-f left-100">
		<div id="MainBox"></div>
	</div>
	<!-- Bitcoin 账号 -->
	<input id="btc-key" type="text" value="#" readonly="readonly">
</body>
</html>

{% endraw %}