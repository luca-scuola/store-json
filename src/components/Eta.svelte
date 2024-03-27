<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';

  let canvasElement;

  // Uso l'hook onMount per caricare i dati non appena il componente viene montato.
  onMount(async () => {
    const response = await fetch('./src/shopping_trends_updated.json');
    if (response.ok) {
      const jsonData = await response.json();
      const ageData = prepareAgeData(jsonData);
      updateBarChart(ageData);
    } else {
      console.error('Failed to fetch data:', response.status);
    }
  });

  // Questa funzione non è più necessaria per il JSON
  // function parseCSV(csvText) { ... }

  function prepareAgeData(items) {
    const ageCounts = items.reduce((acc, item) => {
      const age = item.Age.toString(); // Assicurati che l'età sia una stringa per le etichette del grafico
      if (age) {
        acc[age] = (acc[age] || 0) + 1;
      }
      return acc;
    }, {});

    // Restituisco un oggetto con le etichette (età) e i dati (conteggi) per il grafico.
    return {
      labels: Object.keys(ageCounts).sort((a, b) => a - b), // Ordina le età in modo crescente
      data: Object.values(ageCounts),
    };
  }

  function updateBarChart(ageData) {
    const data = {
      labels: ageData.labels,
      datasets: [{
        label: 'Numero di Compratori per età',
        data: ageData.data,
        backgroundColor: 'rgba(54, 162, 235, 0.5)',
        borderColor: 'rgba(54, 162, 235, 1)',
        borderWidth: 1,
      }]
    };

    const config = {
      type: 'bar',
      data: data,
      options: {
        responsive: true,
        maintainAspectRatio: false,
        animation: {
          duration: 800,
        },
        scales: {
          x: {
            title: {
              display: true,
              text: 'Età'
            }
          },
          y: {
            title: {
              display: true,
              text: 'Numero di Compratori'
            }
          }
        }
      }
    };
    new Chart(canvasElement, config);
  }
</script>
<style> 
  div { 
    margin-top: 20px; 
  } 
  canvas { 
    max-width: 100%; 
    height: 500px; 
    margin: auto;
  }
</style>
<div>
  <canvas bind:this={canvasElement}></canvas>
</div>
