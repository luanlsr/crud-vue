<template>
	<div id="app" class="container">
		<h1>HTTP com Axios</h1>
		<b-alert 
		show dismissible  
		v-for="mensagem in mensagens" 
		:key="mensagem.texto"
		:variant="mensagem.tipo">{{mensagem.texto}}</b-alert>
		<b-card>
			<b-form-group label="Nome">
				<b-form-input
					type="text"
					size="lg"
					v-model="usuario.nome"
					placeholder="Informe o Nome"
				></b-form-input>
			</b-form-group>
			<b-form-group label="Email">
				<b-form-input
					type="email"
					size="lg"
					v-model="usuario.email"
					placeholder="Informe o Email"
				></b-form-input>
			</b-form-group>
			<b-form-group label="Senha">
				<b-form-input
					type="password"
					size="lg"
					v-model="usuario.senha"
					placeholder="Informe a Senha"
				></b-form-input>
			</b-form-group>
				<hr>
				<b-button size="lg" variant="success" @click.prevent="salvar">Salvar</b-button>
				<hr>
				<b-button class="mb-3" size="lg" variant="primary" @click.prevent="obterUsuarios">Obter Usuários</b-button>
				<b-list-group>
					<b-list-group-item v-for="(usuario, id) in usuarios" :key="id">
						<strong>Nome: </strong>{{usuario.nome}} <br>
						<strong>Email: </strong>{{usuario.email}}<br>
						<strong>ID: </strong>{{id}}<br>
						<b-button @click="editar(id)" size="lg" variant="warning">Editar</b-button>
						<b-button variant="danger" class="ml-2" size="lg" @click="excluir(id)">Excluir</b-button>
					</b-list-group-item>
				</b-list-group>
		</b-card>
		<hr>
	</div>
</template>

<script>
export default {
	data() {
		return {
			usuario: {
				nome: '',
				email: '',
				senha: ''
			},
			enviado: false,
			usuarios: [],
			id: null,
			mensagens: []
		}
	},
	methods: {
		limpar() {
			this.usuario = {}
			this.mensagens = []
		},
		salvar() {
			const metodo = this.id ? 'patch' : 'post'
			const finalUrl = this.id ? `/${this.id}.json` : '.json'
			this.enviado = true
			this.$http[metodo](`/usuarios${finalUrl}`, this.usuario)
			.then(() => {
				this.limpar()
				this.mensagens.push({
					texto: 'Salvo com sucesso!',
					tipo: 'success'
				})
			})
		},
		obterUsuarios() {
			this.$http.get('usuarios.json').then(res => {
				this.usuarios = res.data
			})
		},
		editar(id) {
			this.id = id
			this.usuario = {...this.usuarios[id]}
		},
		excluir(id) {
			this.$http.delete(`usuarios/${id}.json`)
			.then(() => {
				this.limpar()
				this.mensagens.push({
					texto: 'Excluído!',
					tipo: 'warning'
				})
			})
			.catch(() => {
				this.mensagens.push({
					texto: 'Problema para excluir',
					tipo: 'danger'
				})
			})
		}
	}
}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	color: #2c3e50;
	font-size: 1.5rem;
}

#app h1 {
	text-align: center;
	margin: 50px;
}
</style>
