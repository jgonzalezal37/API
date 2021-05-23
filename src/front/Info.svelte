<script>
    import {
    Button, Container, Jumbotron,
    
  } from "sveltestrap";
  </script>
  
  <main>
      


    <Jumbotron>
        <p>API DATOS ACEITE</p>
        <a href="#/oil-production-stats"
                ><Button color="primary">Datos aceite (Frontend)</Button></a
              >
        <a href="https://sos2021-02.herokuapp.com/api/v2/oil-production-stats"
              ><Button color="primary">URL base (Backend)</Button></a
            >
        <a href="https://documenter.getpostman.com/view/10673756/TzJoDfUb"
                ><Button color="primary">Documentación de Postman</Button></a
              >
        <a href="https://documenter.getpostman.com/view/10673756/TzRLkqi1"
                ><Button color="primary">Documentación de Postman v2</Button></a
              >
        <a href="#/oil-graph"
                ><Button color="warning">Gráfica</Button></a
              >
        <ul>
        <p1>José Manuel González Domínguez. Fuente: <a href="https://sos2021-02.herokuapp.com/api/v2/oil-production-stats"> oil-production-stats</a></p1>
    </ul>
    </Jumbotron>
    

            <a href="https://github.com/jgonzalezal37"> Repositorio de Github</a>
      
   </main>

   <style>
       p {
            color: rgb(0, 0, 0);
            width: 300px;
            border: 1px solid black;
            
            margin-bottom: 25px;
            margin-left: 43%;
            text-align: center;
          }
      ul {
          margin-top: 25px;
      }
      
   </style>