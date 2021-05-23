<script>
    import {
        onMount
    } from "svelte";
    import {
        pop
    } from "svelte-spa-router";

    import Table from "sveltestrap/src/Table.svelte";
    import Button from "sveltestrap/src/Button.svelte";
    import numData from "./OilTable.svelte";
    export let params = {};
    //ruda de v2
    const BASE_OIL_API_PATH = "/api/v2/";
    let data = {};
    let updateCountry = "XXXX";
    let updateYear = 9999;
    let updateProduction = 9999;
    let updateDistribution = 9999;
    let updateExportation = 9999;
    let errorMsg = "";
    let okMsg = "";

    onMount(getData);

    async function getData(){
        console.log("Fetching oilstats...");
        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats/" + params.country + "/" + params.year);

        if(res.ok){
            console.log("Ok.");
            const json = await res.json();
            data = json;
            updateCountry = data.country;
            updateYear = data.year;
            updateProduction= data["production"];
            updateExportation= data["exportation"];
            updateDistribution= data["distribution"];
            


            console.log(`Data received`);
        }else{
            errorMsg = res.status + ": " + res.statusText;
            console.log("ERROR!" + errorMsg);
        }
    }   
    
//1
    async function updateData() {

        console.log("Updating country..." + JSON.stringify(params.country)+JSON.stringify(params.year));

        const res = await fetch("/api/v2/oil-production-stats/" + params.country + "/" + params.year, {
            method: "PUT",
            body: JSON.stringify({
                "country": params.country,
                "year": parseInt(params.year),
                "production": parseFloat(updateProduction),
                "exportation": parseFloat(updateExportation),
                "distribution": parseFloat(updateDistribution),
            }),
            headers: {
                "Content-Type": "application/json"
            }
        }).then(function (res) {
        if (res.ok) {
          console.log("OK");
          getData();
          
          errorMsg = "";
          okMsg = "Dato actualizado";
        } else {
          errorMsg = "Parametros incorrectos";
          getData();
          console.log("ERROR!" + errorMsg);
        }
      });
    }
    
    onMount(getData);
</script>

<main>
    <h1>
        Editar <strong>{params.country}</strong>
    </h1>
    <div>
        {#if errorMsg}
        <p class="msgRed" style="color: #9d1c24">ERROR: {errorMsg}</p>
    {/if}
        {#if okMsg}
      <p class="msgGreen" style="color: #155724">{okMsg}</p>
    {/if}
    </div>
    <Table bordered>
        <thead>
            <tr>
                <th>Pais</th>
                <th>Año</th>
                <th>Produccion</th>
                <th>Exportacion</th>
                <th>Distribucion</th>
                <th>Actions</th>
                
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>{updateCountry}</td>
                <td>{updateYear}</td>
                <td><input bind:value="{updateProduction}"></td>
                <td><input bind:value="{updateExportation}"></td>
                <td><input bind:value="{updateDistribution}"></td>
                <td><Button on:click={updateData}>Actualizar</Button></td>
            </tr>
        </tbody>
    </Table>
    <Button outline color="secondary" on:click="{pop}">Atrás</Button>
</main>

<style>
    p {
    display: inline;
  }

  .msgRed {
    padding: 4px;
    background-color: #f8d7da;
  }

  .msgGreen {
    padding: 4px;
    background-color: #d4edda;
  }

  div{
    margin-bottom: 15px;
  }
</style>

