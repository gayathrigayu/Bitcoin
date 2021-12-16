<script>
	import { Tabs } from './tab.js';
	import { onMount } from "svelte";
	import { Col, Container, Row } from 'sveltestrap';
	  import Dropdown from 'sv-bootstrap-dropdown';
	
	import { tweened } from 'svelte/motion';
	// List of tab items with labels and values.
	let tabItems = [{
			label: "Buy",
			value: 1
		},
		{
			label: "Sell",
			value: 2
		},
		{
			label: "Convert",
			value: 3
		}
	];
	
	// Current active tab
	// Current active tab
	let currentTab;
	let answer = '';
	let cryptoAmount = 1;
	let currencyAmount;
	const feeCharge = 10;
	let summaryAmount;
	let ApiLoad = false;
	let originalTime = 20;
	let timer = tweened(originalTime)
	let minutes;
	let minname;
	let seconds;
	let percurrecnyamount;
	
	
	setInterval(() => {
		if ($timer > 0) {
			$timer--;
		} else {
			onTimerChange();
		}
	  }, 1000);
	  $:minutes = Math.floor($timer / 60);
	  $:minname = ":";
	  $:seconds = Math.floor($timer - minutes * 60)
	
	const onTimerChange = () => {
		originalTime = 20;
		timer = tweened(originalTime)
		onMount(async () => {
		fetch("https://min-api.cryptocompare.com/data/pricemulti?fsyms=BTC,ETH&tsyms=USD,EUR,GBP,AUD,CAD,JPY,SGD")
			.then(response => response.json())
			.then(data => {
				conversionApiData = data;
				if (cryptoAmount && selectedCrypto && selectedCurrency && conversionApiData) {
					percurrecnyamount = conversionApiData[selectedCrypto][selectedCurrency]
					currencyAmount = conversionApiData[selectedCrypto][selectedCurrency] * Number(cryptoAmount);
				}
				ApiLoad = true;
				calculateSummaryCharge();
			}).catch(error => {
				console.log(error);
				return [];
			});
	});
	}
	
	
	const currencies = [{
			"currencyCode": "USD",
			"imageSrc": "https://demos.creative-tim.com/argon-dashboard-pro/assets/img/icons/flags/US.png"
		},
		{
			"currencyCode": "GBP",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/GB.svg"
		},
		{
			"currencyCode": "SGD",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/SG.svg"
		},
		{
			"currencyCode": "CAD",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/CA.svg"
		},
		{
			"currencyCode": "JPY",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/JP.svg"
		},
		{
			"currencyCode": "EUR",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/EU.svg"
		},
		{
			"currencyCode": "AUD",
			"imageSrc": "https://catamphetamine.gitlab.io/country-flag-icons/3x2/AT.svg"
		}
	];
	const cryptoCurrencies = [{
			"currencyCode": "BTC",
			"imageSrc": "https://i.etsystatic.com/7239025/r/il/c61a06/3317977989/il_794xN.3317977989_de13.jpg"
		},
		{
			"currencyCode": "ETH",
			"imageSrc": "https://upload.wikimedia.org/wikipedia/commons/0/05/Ethereum_logo_2014.svg"
		}
	];
	let selectedCurrency = currencies[0].currencyCode;
	let selectedCrypto = cryptoCurrencies[0].currencyCode;
	let conversionApiData;
	let dropdownTrigger;
	let FirstdropdownTrigger;
	let selectedCryptoMapping;
	
	const onCryptoChange = (value, flag) => {
		if (flag) {
			selectedCurrency = value.currencyCode;
		}
		if (conversionApiData && conversionApiData[selectedCrypto]) {
			if (cryptoAmount) {
				 selectedCryptoMapping = conversionApiData[selectedCrypto];
				 percurrecnyamount = Number(selectedCryptoMapping[selectedCurrency]);
				currencyAmount = Number(selectedCryptoMapping[selectedCurrency]) * Number(cryptoAmount);
				calculateSummaryCharge();
			}
	
		}
	};
	
	const calculateSummaryCharge = () => {
		summaryAmount = Number(currencyAmount) + Number(feeCharge);
	};
	
	const onCryptoCurrencyChange = (value, flag) => {
		if (flag) {
			selectedCrypto = value.currencyCode;
		}
		if (conversionApiData && conversionApiData[value.currencyCode]) {
			if (cryptoAmount) {
				 selectedCryptoMapping = conversionApiData[value.currencyCode];
				 percurrecnyamount = Number(selectedCryptoMapping[selectedCurrency]);
				currencyAmount = Number(selectedCryptoMapping[selectedCurrency]) * Number(cryptoAmount);
				calculateSummaryCharge();
			}
		}
	};
	
	onMount(async () => {
		fetch("https://min-api.cryptocompare.com/data/pricemulti?fsyms=BTC,ETH&tsyms=USD,EUR,GBP,AUD,CAD,JPY,SGD")
			.then(response => response.json())
			.then(data => {
				conversionApiData = data;
				if (cryptoAmount && selectedCrypto && selectedCurrency && conversionApiData) {
					percurrecnyamount = conversionApiData[selectedCrypto][selectedCurrency];
					currencyAmount = conversionApiData[selectedCrypto][selectedCurrency] * Number(cryptoAmount);
				}
				ApiLoad = true;
				calculateSummaryCharge();
			}).catch(error => {
				console.log(error);
				return [];
			});
	});
	</script>
	<svelte:head>
		<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.0/dist/css/bootstrap.min.css">
		<link rel="stylesheet" href="https://pro.fontawesome.com/releases/v5.10.0/css/all.css">
		</svelte:head>
		{#if true == ApiLoad}
		<main>
			<Tabs bind:activeTabValue={currentTab} items={tabItems} />
	
			<code class="language-text"></code>
	
			{#if 1 === currentTab}
			<h3>Tab 1 content</h3>
			{/if}
	
			{#if 2 === currentTab}
			<div class="sel_tab">
				<Row>
					<Col class="col-lg-8">
					<Row>
						<Col class="col-lg-6">
						<label class="col-lg-12" for={cryptoAmount}>Sell</label>
						<input class="col-lg-12" bind:value={cryptoAmount} on:input={e => onCryptoChange(e.target.value, false)} placeholder="enter Amount">
						</Col>
						<Col class="col-lg-6">
						<label class="col-lg-12" for={answer}>Currency</label>
	
						<Dropdown triggerElement={FirstdropdownTrigger}>
							<button
								type="button"
								class="btn btn-secondary dropdown-toggle col-lg-12"
								bind:this={FirstdropdownTrigger}  >
								{#if selectedCrypto === "BTC"}<img class="cryptoimg" alt="BTCImg" src="https://i.etsystatic.com/7239025/r/il/c61a06/3317977989/il_794xN.3317977989_de13.jpg">
								{/if}
								{#if selectedCrypto === "ETH"}<img class="cryptoimg" alt="ETGImg" src="https://upload.wikimedia.org/wikipedia/commons/0/05/Ethereum_logo_2014.svg">
								{/if}
								{selectedCrypto}
							</button>
							<div slot="DropdownMenu">
								{#each cryptoCurrencies as crypto}
								<div class="col-lg-12 flag-return"  on:click={e => onCryptoCurrencyChange(crypto, true)}>
									<img class="cryptoimg" alt="CryptoImage" src="{crypto.imageSrc}">
									<button class="dropdown-item" type="button">{crypto.currencyCode}</button>
								</div>
	
								{/each}
	
							</div>
						</Dropdown>
						</Col>
					</Row>
					<Row>
					<div class="equal-div">
					<div class="equals">=</div>
					 <div class="equals-border"></div>
					</div>
					</Row>
					<Row>
						<Col class="col-lg-6">                      
							 <label class="col-lg-12" for={currencyAmount}>GET</label>
						<input class="col-lg-12" bind:value={currencyAmount} placeholder="Get Amount" disabled="true">
						</Col>
						<Col class="col-lg-6">
						<label class="col-lg-12">Currency</label>
						<Dropdown triggerElement={dropdownTrigger}>
							<button
								type="button"
								class="btn btn-secondary dropdown-toggle col-lg-12"
								bind:this={dropdownTrigger}  >
								<div class={selectedCurrency}></div>{selectedCurrency}
							</button>
							<div slot="DropdownMenu">
								{#each currencies as currency}
								<div class="col-lg-12 flag-return"  on:click={e => onCryptoChange(currency, true)}>
									<img class="cryptoimg" alt="CryptoImage" src="{currency.imageSrc}">
									<button class="dropdown-item" type="button">{currency.currencyCode}</button>
								</div>
	
								{/each}
	
							</div>
						</Dropdown>
	
						</Col>
					</Row>
					</Col>
					<Col class="col-lg-4 summary-box">
					<label class="col-lg-12" for={answer}>Summary</label>
					<div class="exchange col-lg-11">
						<div class="firstexchange col-lg-12">
							<Row>
								<div class="col-lg-8">
									<span class="amount">{percurrecnyamount}</span><span class="c1">{selectedCurrency}</span><span class="per">per</span><span class="c1">{selectedCrypto}</span>
								</div>
								<div class="col-lg-4">
									<i class="fa fa-alarm-clock"></i><span class="time">{minutes}{minname}{seconds}</span>
								</div>
							</Row>
						</div>
						<div class="exchange-new col-lg-12">
							<div class="secondexchange col-lg-12">
								<Row>
									<div class="col-lg-8 sell">
										<span class="amount">{cryptoAmount}</span><span class="c1">{selectedCrypto}</span>
									</div>
									<div class="col-lg-4 get">
										<span class="amount">{currencyAmount}</span><span class="c1">{selectedCurrency}</span>
									</div>
								</Row>
								<div class="exchange-fee col-lg-12">
									<Row>
										<div class="col-lg-8 fees">
											<span class="feeamount">Fee:</span>
										</div>
										<div class="col-lg-4 feeget">
											<span class="amount">{feeCharge}</span><span class="c1">{selectedCurrency}</span>
										</div>
									</Row>
								</div>
							</div>
							<div class="totalexchange col-lg-12">
								<Row>
									<div class="col-lg-8 total">
										<span class="total">Total:</span>
									</div>
									<div class="col-lg-4 total">
										<span class="amount">{summaryAmount}</span><span class="c1">{selectedCurrency}</span>
									</div>
								</Row>
							</div>
							<button class="sell-btn">Sell</button>
						</div>
	
					</div>
				 
	
					</Col>
				</Row>
	
				<Row>
	
				</Row>
			</div>
			{/if}
	
			{#if 3 === currentTab}
			<h3>Tab 3 content</h3>
			{/if}
		</main>
		{/if}