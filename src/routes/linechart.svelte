<!-- LineChart.svelte -->
<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  // Données d'exemple pour le graphique
  const data = [
    { date: '2023-01', value: 30 },
    { date: '2023-02', value: 45 },
    { date: '2023-03', value: 25 },
    { date: '2023-04', value: 60 },
    { date: '2023-05', value: 40 },
    { date: '2023-06', value: 80 },
    { date: '2023-07', value: 70 },
    { date: '2023-08', value: 90 },
    { date: '2023-09', value: 85 }
  ];

  // Dimensions et marges du graphique
  const margin = { top: 20, right: 30, bottom: 50, left: 50 };
  const width = 800 - margin.left - margin.right;
  const height = 400 - margin.top - margin.bottom;

  onMount(() => {
    createChart();
  });

  function createChart() {
    // Nettoyage au cas où le graphique existe déjà
    d3.select('#chart-container svg').remove();

    // Création du SVG
    const svg = d3.select('#chart-container')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    // Échelles X et Y
    const x = d3.scaleBand()
      .domain(data.map(d => d.date))
      .range([0, width])
      .padding(0.1);

    const y = d3.scaleLinear()
      .domain([0, d3.max(data, d => d.value) * 1.1]) // Ajoute 10% d'espace au-dessus
      .range([height, 0]);

    // Axe X
    svg.append('g')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(x))
      .selectAll('text')
      .style('text-anchor', 'end')
      .attr('dx', '-.8em')
      .attr('dy', '.15em')
      .attr('transform', 'rotate(-45)');

    // Titre axe X
    svg.append('text')
      .attr('transform', `translate(${width/2}, ${height + margin.bottom - 10})`)
      .style('text-anchor', 'middle')
      .text('Période');

    // Axe Y
    svg.append('g')
      .call(d3.axisLeft(y));

    // Titre axe Y
    svg.append('text')
      .attr('transform', 'rotate(-90)')
      .attr('y', -margin.left + 15)
      .attr('x', -height / 2)
      .style('text-anchor', 'middle')
      .text('Valeur');

    // Création de la ligne
    const line = d3.line()
      .x(d => x(d.date) + x.bandwidth() / 2)
      .y(d => y(d.value))
      .curve(d3.curveMonotoneX); // Lissage de la courbe

    // Ajout de la ligne au graphique
    svg.append('path')
      .datum(data)
      .attr('fill', 'none')
      .attr('stroke', '#2563eb') // Couleur bleue
      .attr('stroke-width', 2)
      .attr('d', line);

    // Ajout des points sur la ligne
    svg.selectAll('.dot')
      .data(data)
      .enter()
      .append('circle')
      .attr('class', 'dot')
      .attr('cx', d => x(d.date) + x.bandwidth() / 2)
      .attr('cy', d => y(d.value))
      .attr('r', 5)
      .attr('fill', '#2563eb')
      .attr('stroke', '#fff')
      .attr('stroke-width', 2);

    // Ajout d'une animation pour les tooltips au survol
    svg.selectAll('.dot')
      .on('mouseover', function(event, d) {
        d3.select(this)
          .transition()
          .duration(200)
          .attr('r', 8);

        svg.append('text')
          .attr('class', 'tooltip')
          .attr('x', x(d.date) + x.bandwidth() / 2 + 5)
          .attr('y', y(d.value) - 10)
          .attr('text-anchor', 'middle')
          .style('font-size', '12px')
          .style('font-weight', 'bold')
          .text(`${d.value}`);
      })
      .on('mouseout', function() {
        d3.select(this)
          .transition()
          .duration(200)
          .attr('r', 5);
          
        svg.selectAll('.tooltip').remove();
      });
  }
</script>

<div class="chart-wrapper">
  <h2>Évolution sur 9 mois</h2>
  <div id="chart-container"></div>
</div>

<style>
  .chart-wrapper {
    font-family: Arial, sans-serif;
    margin: 20px auto;
    max-width: 800px;
  }
  
  h2 {
    text-align: center;
    color: #333;
    margin-bottom: 20px;
  }
  
  #chart-container {
    background-color: #f9fafb;
    border-radius: 8px;
    padding: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }
</style>

