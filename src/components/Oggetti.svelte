<script>
  import { onMount } from 'svelte';

  let items = [];
  let categories = new Set();
  let filterCategory = '';
  let filterPrice = '';

  onMount(async () => {
    const response = await fetch('./src/shopping_trends_updated.json');
    if (response.ok) {
      const jsonData = await response.json();
      items = jsonData.slice(0, 3900); // Assumi che jsonData sia un array di oggetti
      items.forEach(item => categories.add(item.Category));
      items.sort((a, b) => a.Category.localeCompare(b.Category));
    } else {
      console.error('Failed to fetch data:', response.status);
    }
  });

  // Elimina la funzione parseCSV poiché non è più necessaria

  $: filteredAndSortedItems = items.filter(item => {
    const passesCategoryFilter = !filterCategory || item.Category === filterCategory;
    const passesPriceFilter = !filterPrice || parseFloat(item['Purchase Amount (USD)']) <= parseFloat(filterPrice);
    return passesCategoryFilter && passesPriceFilter;
  });
</script>

<div>
  <select bind:value={filterCategory}>
    <option value="">All Categories</option>
    {#each Array.from(categories) as category}
      <option value={category}>{category}</option>
    {/each}
  </select>
  <input type="number" bind:value={filterPrice} placeholder="Max Price (USD)" />
</div>

{#if filteredAndSortedItems.length > 0}
  <div class="table-container">
    <table>
      <thead>
        <tr>
          <th>Category</th>
          <th>Item Purchased</th>
          <th>Purchase Amount (USD)</th>
        </tr>
      </thead>
      <tbody>
        {#each filteredAndSortedItems as item}
          <tr>
            <td>{item.Category}</td>
            <td>{item['Item Purchased']}</td>
            <td>{item['Purchase Amount (USD)']}</td>
          </tr>
        {/each}
      </tbody>
    </table>
  </div>
{:else}
  <p>No items to display based on filter.</p>
{/if}

<style>
  .table-container {
    overflow-x: auto;
    margin: 0 auto; 
    text-align: center;
  }
  table {
    border-collapse: separate; 
    border-spacing: 0 5px; 
    margin: 0 auto; 
    width: auto; 
  }
  th, td {
    padding: 8px;
    text-align: center; 
  }
  th {
    background-color: #f0f0f0;
  }
  tr:nth-child(odd) td {
    background-color: #fafafa; 
  }
  tr:nth-child(even) td {
    background-color: #f5f5f5;
  }
</style>
