<script>
    import {
        onMount
    } from "svelte";
    import {
        pop
    } from "svelte-spa-router";
    import Button from "sveltestrap/src/Button.svelte";
 
    //let generalData = [];   
    let oilData = [];
    let ejeX = [];
    let production = [];
    let exportation = [];
    let distribution = [];

    async function loadGraph(){
        const oil = await fetch("/api/v2/oil-production-stats");
        if(oil.ok){
            oilData = await oil.json();
            console.log(`We have received ${oilData.length} data points: `);
            console.log(JSON.stringify(oilData,null,2));
            oilData.forEach(data => {
                ejeX.push(data.country + "-" + data.year);
                production.push(data["production"]);
                exportation.push(data["exportation"]);
                distribution.push(data["distribution"]);
            });
        }else{
            console.log("Error loading oilData");
        }
    
        Highcharts.chart('container', {
            title: {
                text: 'Oil Graph'
            },
            yAxis: {
                title: {
                    text: 'Cantidad'
                }
            },
            xAxis: {
                title: {
                    text: 'País-Año'
                },
                categories: ejeX
            },
            legend: {
                layout: 'vertical',
                align: 'right',
                verticalAlign: 'middle'
            },/*
            plotOptions: {
                series: {
                    label: {
                        connectorAllowed: false
                    },
                    pointStart: 2010
                }
            },*/
            series: [{
                name: 'Production',
                data: production
            },
            {
                name: 'Exportation',
                data: exportation
            },
            {
                name: 'Distribution',
                data: distribution
            }],
            responsive: {
                rules: [{
                    condition: {
                        maxWidth: 500
                    },
                    chartOptions: {
                        legend: {
                            layout: 'horizontal',
                            align: 'center',
                            verticalAlign: 'bottom'
                        }
                    }
                }]
            }
        });
  }
</script>

<svelte:head>
    <script src="https://code.highcharts.com/highcharts.js"></script>
    <script src="https://code.highcharts.com/modules/series-label.js"></script>
    <script src="https://code.highcharts.com/modules/exporting.js"></script>
    <script src="https://code.highcharts.com/modules/export-data.js"></script>
    <script src="https://code.highcharts.com/modules/accessibility.js" on:load="{loadGraph}"></script>
</svelte:head>

<main>
    <figure class="highcharts-figure">
        <div id="container"></div>
        
    </figure>
    <Button outline color="secondary" on:click="{pop}">Atrás</Button>
</main>