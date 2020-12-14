<template>
    <div>
      <div id="topo">
				
				<div id="topo" class="container">

					<h2>CADASTRE-SE</h2>
					<p class="branco">Para se cadastrar, preencha o formulário</p>

					<div class="row">

            <form class="form-inline" @submit.prevent="onSubmit">
              <div class="col-md-3 mb-2">
                <div class="form-group">
                  <input type="text" name="nome" id="nome" class="form-control" placeholder="Nome" v-model="nome">
                </div>
              </div>

              <div class="col-md-3 mb-2">
                <div class="form-group">
                  <input type="text" name="sobrenome" id="sobrenome" class="form-control" placeholder="Sobrenome" v-model="sobrenome">
                </div>
              </div>

              <div class="col-md-3 mb-2">
                <div class="form-group">
                  <input type="text" name="participacoes" id="participacoes" class="form-control" placeholder="Participações" v-model="participacoes">
                </div>
              </div>
              
              <div class="col-md-3 mb-2">
                <div class="form-group">
                  <button class="btn btn-primary" type="submit">ENVIAR</button>
                </div>
              </div>
            </form>

					</div>
					
				</div>

			</div>

			<div style="margin-bottom: 100px;" class="container">

				<h3>DADOS</h3>
				<p style="margin-bottom: 80px;">Informações dos dados abaixo</p>
					
				<div class="row">
						
					<div class="col-md-6">

            <table border="1" class="table table-bordered">
              <thead>
                <tr>
                  <th style="text-align: center;">#</th>
                  <th style="text-align: center;">Nome</th>
                  <th style="text-align: center;">Sobrenome</th>
                  <th style="text-align: center;">Participações</th>
                </tr>
              </thead>
              <tbody>
                <tr style="text-align: center;" v-for="(titulo, idx) in titulos" :key="idx">
                  <td>{{ idx + 1 }}</td>
                  <td>{{ titulo.nome }}</td>
                  <td>{{ titulo.sobrenome }}</td>
                  <td>{{ titulo.participacoes }}%</td>
                </tr>
              </tbody>
            </table>

					</div>

					<div class="col-md-6">

            <canvas id="myChart"></canvas>

					</div>

				</div>
		
			</div>

    </div>
</template>

<script>
import Chart from 'chart.js';

export default {
  name: 'Home',
  data () {
    return {
      nome: '',
      sobrenome: '',
      participacoes: '',
      titulos: [],
      myChart: null,
      data: {
        labels: [
              
        ],
        datasets: [{
            data: [],
            backgroundColor: []
        }]
      }
    }
  },
  methods: {
    validarForm() {
      if (this.nome && this.sobrenome && this.participacoes) {
        return true;
      }

      if (!this.name) {
        alert('Nome é um atributo obrigatorio')
      }
      if (!this.sobrenome) {
        alert('Sobrenome é um atributo obrigatorio')
      }
      if (!this.participacoes) {
        alert('Participações é um atributo obrigatorio')
      }

      return false
    },
    cor_random(){
      let color = '#';

      let digits = [
        '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F'
      ];

      for(let i = 0; i < 6; i++){
        let index = Math.floor(Math.random()*14);
        color += digits[index];
      }

      return color;
    },
    createChart(chartId, chartData) {
      let ctx = document.getElementById(chartId).getContext('2d');
       this.myChart = new Chart(ctx, {
        type: 'doughnut',
        data: chartData,
        options: chartData,
      });
    },
    getUsers() {
      this.axios.get('users')
      .then(res => {
        this.titulos = res.data
        for (var i = 0; i < this.titulos.length; i++) {
          this.data.labels[i] = this.titulos[i].nome;
          this.data.datasets[0].data[i] = this.titulos[i].participacoes;
          this.data.datasets[0].backgroundColor[i] = this.cor_random();
        }
      })
      .catch(e => {
        alert(e.response.data.name + ': ' + e.response.data.message)
      })
      .finally(() => {
        this.createChart('myChart', this.data)
      })
    },
    onSubmit() {
      const validador = this.validarForm()
      if(validador){
        this.axios.post('users', {
          nome: this.nome,
          sobrenome: this.sobrenome,
          participacoes: this.participacoes
        })
        .then(() => {
          alert('Incluido com sucesso')
          this.getUsers()
        })
        .catch((e) => {
          alert(e.response.data.error)
        })
        .finally(() => {
          location.reload()
        })
      }
    },
  },
  mounted: async function() {
    await this.getUsers()
  }
}
</script>

<style scoped>
  #topo{
    background-color: #87CEEB;
    margin-bottom: 100px;
  }

  h2{
    text-align: center;
    color: white;
  }

  .branco{
    text-align: center;
    color: white;
  }

  #btn-enviar{
    background-color: #87CEEB;
    border-color: white;
  }

  h3, p{
    text-align: center;
  }

</style>