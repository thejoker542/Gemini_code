<script>
  import { onMount, createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  const exSymbols = ['NIFTY', 'BANKNIFTY', 'MIDCAPNIFTY', 'SENSEX', 'BANKEX'];
  let selectedExSymbol = 'NIFTY';

  // Parse expiry dates from the sample data
  const sampleData = "NSE:NIFTY2510930000CE,NIFTY,11,10,2025-01-09 10:00:00,30000.0,NIFTY2510930000CE";
  const expiryDateValue = sampleData.split(',')[4].split(' ')[0];
  const expiryDates = [expiryDateValue];
  let selectedExpiryDate = expiryDates[0];

  const strikePrices = Array.from({ length: 11 }, (_, i) => 24000 + (i - 5) * 100);
  let selectedStrikePrice = 24000;

  const timeframes = [1, 3, 5, 15, 30];
  let selectedTimeframe = 5;

  async function fetchData() {
    const expiryDateParts = selectedExpiryDate.split('-');
    const formattedExpiryDate = `${expiryDateParts[0].slice(-2)}${expiryDateParts[1]}${expiryDateParts[2]}`;
    const ceSymbol = `NSE:${selectedExSymbol}${formattedExpiryDate}${selectedStrikePrice}CE`;
    const peSymbol = `NSE:${selectedExSymbol}${formattedExpiryDate}${selectedStrikePrice}PE`;

    dispatch('chartButtonClick', {
      exSymbol: selectedExSymbol,
      expiryDate: selectedExpiryDate,
      strikePrice: selectedStrikePrice,
    });
  }

  function handleTimeframeChange() {
    dispatch('timeframeChange', { timeframe: selectedTimeframe });
  }

  onMount(fetchData);
</script>

<div>
  <label for="exSymbol">ExSymbol:</label>
  <select id="exSymbol" bind:value={selectedExSymbol}>
    {#each exSymbols as symbol}
      <option value={symbol}>{symbol}</option>
    {/each}
  </select>

  <label for="expiryDate">Expiry Date:</label>
  <select id="expiryDate" bind:value={selectedExpiryDate}>
    {#each expiryDates as date}
      <option value={date}>{date}</option>
    {/each}
  </select>

  <label for="strikePrice">Strike Price:</label>
  <select id="strikePrice" bind:value={selectedStrikePrice}>
    {#each strikePrices as price}
      <option value={price}>{price}</option>
    {/each}
  </select>

  <label for="timeframe">Timeframe:</label>
  <select id="timeframe" bind:value={selectedTimeframe} on:change={handleTimeframeChange}>
    {#each timeframes as timeframe}
      <option value={timeframe}>{timeframe} min</option>
    {/each}
  </select>

  <button on:click={fetchData}>Chart</button>
</div>