<script>
    import {
        onMount
    } from "svelte";
    import {
        pop
    } from "svelte-spa-router";
    import {
        Modal, ModalBody, ModalFooter, ModalHeader
    } from "sveltestrap";

    import Table from "sveltestrap/src/Table.svelte";
    import Button from "sveltestrap/src/Button.svelte";
    import { get } from "svelte/store";
    var BASE_OIL_API_PATH = "/api/v2/";
    let oilstats = [];
    let newCountry = {
        country: "",
        year: "",
        "production": "",
        "exportation": "",
        "distribution": "",
    }

    let searchCountry = {
        country: "",
        year: "",
        "production": "",
        "exportation": "",
        "distribution": "",
    }
    let errorMsg = "";
    let okMsg = "";

    let limit = 10;
	let offset = 0;
    let actual = 1;
    let num_paginas = 0;
    let pagina = (offset/10)+1;
    //let numData = 5;

    let open = false;
	let size = "";
	const toggle = () =>{
        size = '25px';
		open = !open;
	}

    async function loadData(){
        console.log("Loading oilstats...");
        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats/loadInitialData");

        if(res.ok){
            console.log("Ok.");
            getData();
            //numData = 5;
            errorMsg = "";
            okMsg = "Datos cargados correctamente.";
        }else{
            console.log("Error!");
        }
    }   

    async function getNumPaginas() {
        console.log("Fetching oilstats...");
        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats");
        let datos=[]
        if(res.ok){
            const json = await res.json();
            datos = json;
            num_paginas=(datos.length/10)+1|0;
            if(datos.length%10==0&&num_paginas!==1){
                num_paginas--;
            }
        }
        else{
            console.log("ERROR!");
        }
    }

    async function getData(){
        getNumPaginas();
        console.log("Fetching oilstats...");
        var url = BASE_OIL_API_PATH+"oil-production-stats?limit="+limit+"&offset="+offset;//*limit);
        const res = await fetch(url);

        if(res.ok){
            console.log("Ok.");
            const json = await res.json();
            oilstats = json;
            pagina = (offset/10)+1;
            console.log(`We have received ${oilstats.length} countries.`);
            console.log("pagina="+pagina+"num_pag="+num_paginas);
        }else{
            console.log("Error!");
        }
    }   
    
    onMount(getData);

    async function insertCountry(){
        console.log("Inserting country "+ JSON.stringify(newCountry));
        newCountry.country = (newCountry.country);
        newCountry.year = parseInt(newCountry.year);
        newCountry["production"] = parseFloat(newCountry["production"]);
        newCountry["exportation"] = parseFloat(newCountry["exportation"]);
        newCountry["distribution"] = parseFloat(newCountry["distribution"]);

        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats",
                            {
                                method: "POST",
                                body: JSON.stringify(newCountry),
                                headers:{
                                    "Content-Type": "application/json"
                                }
                            }
                           ).then( (res) => {
                               if(res.ok) {
                                //numData++;
                                //console.log("NUMDATA IS:" + numData);
                            getData();
                            okMsg = `${newCountry.country} ${newCountry.year} ha sido insertado correctamente.`
                            errorMsg = "";
                               }else{
                                   if(res.status === 409){
                                       okMsg = "";
                                       errorMsg = `${newCountry.country} ${newCountry.year} ya se encuentra cargado.`
                                   }if(res.status === 400){
                                        okMsg = "";
                                        errorMsg = "Entrada de datos incorrecta";
                                        newCountry.country = "";
                                        newCountry.year = "";
                                        newCountry["production"] = "";
                                        newCountry["exportation"] = "";
                                        newCountry["distribution"] = "";
                                   }

                                   
                                   console.log("ERROR!" + errorMsg);
                               }
                            
                           })
    }

    async function deleteCountry(country, year){
        console.log("Deleting country "+ country+"/"+ year);

        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats/"+country + "/" + year,
                            {
                                method: "DELETE",
                            }
                           ).then( (res) => {
                               if(res.ok) {
                                //numData--;
                                if(oilstats.length==1&&num_paginas>1){
                         offset-=10; getData()
                      }else{
                         getData();
                     }
                                //console.log("NUMDATA IS:" + numData);
                            getData();
                            okMsg = `${country} ${year} ha sido eliminado correctamente.`
                            errorMsg = "";
                               }else{
                                   if(res.status === 404){
                                       okMsg = "";
                                       errorMsg = `${country} ${year} no se encuentra en la base de datos.`
                                   }
                                   console.log("ERROR!" + errorMsg);
                               }
                            
                           })
    }

    async function deleteAllCountries(){
        console.log("Deleting all countries ");

        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats/",
                            {
                                method: "DELETE",
                            }
                           ).then(function (res) {
                                if (res.ok) {
                                    console.log("OK");
                                    oilstats = [];
                                    getData();
                                    errorMsg = "";
                                    okMsg = "Datos borrados correctamente";
                                } else {
                                    if(res.status===404){
                                    okMsg = "";
                                    errorMsg = "No existen datos que borrar";
                                    }else if(res.status ===500){
                                    okMsg = "";
                                    errorMsg = "No se han podido acceder a la base de datos";
                                    }        
                                    okMsg = "";
                                    console.log("ERROR!" + errorMsg);
                                }
                                });
  }
    

    /*async function nextPage() {
        if (offset+10 > numData) {
            if(numdata!=0){
                offset += numData;
            actual = 2;
            }
        } else {
            offset +=10
            actual = 2;
        }
        console.log("Offset: "+ offset);
        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats?limit="+limit+"&offset="+offset);
        if (res.ok) {
            console.log("Ok:");
            const json = await res.json();
            oilstats = json;
            console.log("Received " + oilstats.length + " data.");
        } else {
            console.log("ERROR!");
        }
    }

    async function previousPage() {
 
        if (offset-10>=1) {
            offset -= 10; 
            actual -= 1;
        } else {
            offset = 0
            actual = 1;
        }
        console.log("Offset: " +offset);
        const res = await fetch(BASE_OIL_API_PATH+"oil-production-stats?limit="+limit+"&offset="+offset);
        if (res.ok) {
            console.log("Ok:");
            const json = await res.json();
            oilstats = json;
            console.log("Received " + oilstats.length + " data.");
        } else {
            console.log("ERROR!");
        }
    }
*/
        //paginacion
        const siguiente= () => {offset+=10; getData()}
        const anterior= () => {offset-=10; getData()}

        
    async function searchCountries(offset) {
		let url = "/api/v2/oil-production-stats?limit=10&offset="+ offset;
		console.log("Searching countries...");
		let data = {
			country: searchCountry.country,
			year: parseInt(searchCountry.year),
			production: parseFloat(searchCountry.production),
			exportation: parseFloat(searchCountry.exportation),
			distribution: parseFloat(searchCountry.distribution),
			
		};
		Object.entries(data).forEach(([x,y]) => {
			if(y){
				url = url + "&" + x + "=" + y;
			}
		});
		/*if(data.country){
			url = url +"&country=" + data.country;
		};*/
		console.log("esta es la url:"+url);
		const res = await fetch(url);
		if (res.ok) {
			console.log("Ok:");
			const json = await res.json();
			oilstats = json;
			console.log("Received " + oilstats.length + " countries.");
			if (oilstats.length > 0){
				okMsg = "Se ha realizado la búsqueda.";
				errorMsg = false;
			}else{
				okMsg = false;
				errorMsg = "La búsqueda no ha obtenido resultados.";
			};
			
		} else {
			console.log("ERROR!");
			okMsg = false;
			errorMsg = "La búsqueda no ha obtenido resultados.";
		};
	};

    onMount(getData);
</script>

<main>
    <h1>
        Datos del Aceite
    </h1>
    <div>
        {#if errorMsg}
        <p class="msgRed" style="color: #9d1c24">ERROR: {errorMsg}</p>
    {/if}
        {#if okMsg}
      <p class="msgGreen" style="color: #155724">{okMsg}</p>
    {/if}
    </div>

    <Table>
        <thead>
            <tr>
                <th>Pais</th>
                <th>Año</th>
                <th>Produccion</th>
                <th>Exportacion</th>
                <th>Distribucion</th>
                <th>Acciones</th>
                
            </tr>
        </thead>
        <tbody>
            <tr>
                    <td><input bind:value="{searchCountry.country}"></td>
                    <td><input bind:value="{searchCountry.year}"></td>
                    <td><input bind:value="{searchCountry['production']}"></td>
                    <td><input bind:value="{searchCountry['exportation']}"></td>
                    <td><input bind:value="{searchCountry['distribution']}"></td>
                    <td><Button outline color= "warning" on:click={searchCountries}>Buscar</Button></td>
            </tr>
        </tbody>
        </Table>

    <Table bordered>
        <thead>
            <tr>
                <th>Pais</th>
                <th>Año</th>
                <th>Produccion</th>
                <th>Exportacion</th>
                <th>Distribucion</th>
                <th>Acciones</th>
                
            </tr>
        </thead>
        <tbody>
            <tr>
                <td><input bind:value="{newCountry.country}"></td>
                <td><input bind:value="{newCountry.year}"></td>
                <td><input bind:value="{newCountry['production']}"></td>
                <td><input bind:value="{newCountry['exportation']}"></td>
                <td><input bind:value="{newCountry['distribution']}"></td>
                <td><Button outline color = "primary" on:click={insertCountry}>Insertar</Button></td>
            </tr>
            {#each oilstats as data}
                <tr>
                    <td><a href="#/oil-production-stats/{data.country}/{data.year}">{data.country}</a></td>
                    <td>{data.year}</td>
                    <td>{data["production"]}</td>
                    <td>{data["exportation"]}</td>
                    <td>{data["distribution"]}</td>
                    <td><Button outline color = "danger" on:click={deleteCountry(data.country, data.year)}>Borrar</Button></td>
                </tr>
            {/each}
        </tbody>
    </Table>
                
    {#if oilstats.length !== 0}
        <div style="text-align: right; " >
            {#if pagina >1}
            <Button outline color="info" on:click={anterior}>Anterior</Button>
            {/if}
            <Button color="dark" >Página nº: {pagina}</Button>    
            {#if num_paginas-pagina !=0 }
             <Button outline color="info" on:click={siguiente}>Siguiente</Button>
             {/if}
        </div>
        {/if}

    
    
    <Button outline color="secondary" on:click="{pop}">Atrás</Button>
    <Button outline color="primary" on:click="{loadData}">Cargar datos iniciales</Button>
    <Button outline color="danger" on:click="{deleteAllCountries}">Borrar todos los datos</Button>
</main>

<style>
    p {
    display: inline;
  }

  .msgRed {
    padding: 8px;
    background-color: #f8d7da;
  }

  .msgGreen {
    padding: 8px;
    background-color: #d4edda;
  }

  div{
    margin-bottom: 15px;
  }

  
</style>

