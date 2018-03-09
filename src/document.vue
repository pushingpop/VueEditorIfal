<template>
	<div id="app">
		<div class="row" style="margin-bottom: 1em;">
			<!-- <div class="col-8">
				<button
					v-for="page in subPages"
					@click.prevent="currentPage = page.id"
					class="btn btn-sm btn-primary mr-1 mb-1"
				>
					{{ page.name }}
				</button>
			</div> -->

			<!-- <div class="col" style="text-align: right;">
				<router-link
					to="/management"
					class="btn btn-sm btn-primary"
				>Salvar{{ document.status == 7 ? ' revisão' : null }} e sair</router-link>
				<router-link
					:to="document.status == 1 ? 'metadata' : 'abstract'"
					class="btn btn-sm btn-primary"
				>Voltar</router-link>
				<a
					v-if="document.status == 7 || 1 == 1"
					@click.prevent="submit"
					class="btn btn-sm btn-success"
					href="#"
				>Submeter{{ document.status == 7 ? ' revisão' : null }}</a>
			</div> -->
		</div>
		<div class="row">
			<div class="col">
				<div v-if="loadedImages.length" class="pb-2">
					{{ currentPage }}
					<b>Sumário de imagens</b>

					<div v-for="image in loadedImages" class="row mb-1">
						<div class="col col-auto" :style="{
							width: '50px',
						}">
							<img :src="baseImage + image.image.name" :style="{
								maxHeight: '20px',
								maxWidth: '20px',
							}">
						</div>

						<div class="col">
							<span :style="{
								backgroundColor: 'gray',
								color: 'white',
							}">Fig {{ image.position }}:</span>
							{{ image.title }}
						</div>

						<div class="col col-auto">
							<button @click="editImage(image)" class="btn btn-secondary">
								<i class="fa fa-pencil" aria-hidden="true"></i>
							</button>

							<button @click="removeImage(image)" class="btn btn-secondary">
								<i class="fa fa-trash" aria-hidden="true"></i>
							</button>
						</div>
					</div>
				</div>

				<input id="uploadImageElement" type="file" :style="{
					display: 'none',
				}" accept="image/*" @change="uploadImage">

				<div v-for="page in subPages">
					<div class="row">
						<div class="col">
							<div class="form-group">
								<label class="form-control-label">
									{{ page.name }}
								</label>

								<label @click="currentPage = page.id" for="uploadImageElement" v-if="!loadingInitGET" :style="{
									height: '43px',
									position: 'absolute',
									lineHeight: '43px',
									marginLeft: '174px',
									display: 'block',
								}" class="tool">
										<i class="fa fa-image"></i>
								</label>

								<div v-if="!loadingInitGET" :style="{
									height: '43px',
									position: 'absolute',
									lineHeight: '43px',
									marginLeft: '205px',
								}" class="tool">
									<i class="fa fa-table"></i>
								</div>

								<div v-if="!loadingInitGET" :style="{
									height: '43px',
									position: 'absolute',
									lineHeight: '43px',
									marginLeft: '231px',
								}" class="tool">
									<i class="fa fa-cogs"></i>
								</div>

								<div v-if="!loadingInitGET" :style="{
									height: '43px',
									position: 'absolute',
									lineHeight: '43px',
									marginLeft: '260px',
								}" class="tool">
									<i class="fa fa-quote-left"></i>
								</div>

								<div v-if="loadingInitGET">
									<center>
										<img src="img/ajax-loader.gif" style="height: 30px" />
									</center>
								</div>

								<vue-editor v-else
									:id="page.id"
									v-model="page.value"
									:editorToolbar="customToolbar"
									:input="updateInTime(page)"
								></vue-editor>

								<small class="form-text text-muted row justify-content-md-center"></small>
							</div>
						</div>
					</div>
				</div>
			</div>

			<div :class="{'col': 1, 'view': 1, 'error': pages > 5,  'd-flex': 1, 'flex-column': 1, }">
				<div class="p-2 header">
					<div class="col">
						Páginas {{ pages }}/5
						<span v-if="pages > 5" class="error"><br>Quantidade de página excedeu o limite permitido</span>
					</div>
				</div>

				<div class="p-2 align-self-stretch document-content" style="overflow: auto; height: 500px;">
					<v-page ref="page-view" :content="content || ''" :receivePages="receivePages" :data="data || {}"></v-page>
				</div>
			</div>
		</div>

		<div class="modal fade" id="exampleModalLong" tabindex="-1" role="dialog" aria-labelledby="exampleModalLongTitle" aria-hidden="true">
			<div class="modal-dialog modal-lg" role="document">
				<div class="modal-content">
					<div class="modal-header">
						<h5 class="modal-title" id="exampleModalLongTitle">Modelos de Referências</h5>
						<button type="button" class="close" data-dismiss="modal" aria-label="Close">
							<span aria-hidden="true">&times;</span>
						</button>
					</div>
					<div class="modal-body" style="font-size: 10pt;">
						<div class="row">
							<div class="col">
								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Genérico</h5>
								Autor. <b>Título</b> |. Título secundário | Autor secundário |. Lugar publicado |: Editora |. Volume |: Páginas p. | Ano.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Livro</h5>
								Autor. <b>Título</b>. Edição. Lugar publicado: | Editora, | Ano. | Número de páginas | ISBN ISBN. | Disponível em: < URL>. | Acesso em: Data de acesso.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Capítulo de Livro</h5>
								Autor. Título |. Em: Editor (Ed.) |. <b>Título do livro</b> | Edição | Lugar publicado |: Editora |, v.Volume |, Ano. | Capítulo, | P. Páginas. | (Título da série). ISBN ISBN.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Conferência</h5>
								Autor. <b>Título</b>. Em: Editor |, Nome da Conferência, |, Ano da Conferência |, Localização da Conferência. | Edição: | Editor |, Data |. P.Páginas.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Artigo</h5>
								Autor. Título. <b>Jornal</b>|, |, v. Volume|, n. Edição|, p. Páginas|, Data| Ano.| ISSN ISSN.| Disponível em: < URL >.| Acesso em : Data de acesso.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Relatório Técnico</h5>
								Autor. <b>Título</b>. Título da coleção |. Lugar publicado: Data |, p. Páginas |. Ano |. (Tipo de trabalho).

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Tese/Dissertação/TCC</h5>
								Autor. <b>Título</b>. Ano.| Número de páginas| Tipo de tese (Graduação/Dissertação/Tese/Relatório de pós-doutorado).| Departamente acadêmico, Universidade|, Lugar de publicação.

								<h5 style="font-size: 14pt; margin-top: 10px; margin-bottom: 10px;">Web page</h5>
								Autor. Título. <b>Título da série</b> |, Lugar publicado |, p. Descrição |, Última data de atualização | Ano. | ISBN do ISSN. `Disponível em: <` URL>. | `Acesso em:` Data de acesso.
							</div>
						</div>
					</div><!--
					<div class="modal-footer">
						<button type="button" class="btn btn-secondary" data-dismiss="modal">Cancelar</button>
						<button @click="updateUserPassword" type="button" class="btn btn-success">Alterar Senha</button>
					</div> -->
				</div>
			</div>
		</div>

		<div class="modal fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
			<AddImage
				:type="typeImageModal"
				:data="returnOfAddImage"
				:onClose="() => { closeModal('#myModal') }"
				:onAddImage="onAddImage"
				:onEditImage="onEditImage"
				:id="currentImageId"
				:values="addImageInput"
			/>
		</div>
	</div>
</template>

<script>

	var element = null

	function ondragenter (event) {
		if (element) {
			element.parentNode.removeChild(element)
			event.srcElement.parentNode.insertBefore(element, event.srcElement.nextSibling)
		}
	}

	function ondragstart () {
		element = event.srcElement;
	}

	import { VueEditor } from 'vue2-editor'
	import Page from '../../component/page.vue'

	import AddImage from './component/add-image.vue'

	export default {
		name: 'Document',

		props: ['do', 'Store', 'document'],

		components: {
			'vue-editor': VueEditor,
			'v-page': Page,
			'AddImage': AddImage,
		},

		data () {
			return {
				currentPage: '',

				currentImageId: '',
				typeImageModal: '',

				loadedImages: [],

				returnOfAddImage: {
					"id": 6,
					"name": "1518185439-S8qeAPMGCnYc7gActOtAUXF7BTJhYzMK0bQAODTU.jpg"
				},

				addImageInput: {
					title: '',
					source: '',
					info: '',
				},

				customToolbar: [
					['bold', 'italic', 'code-block'],
					[{ 'list': 'ordered'}, { 'list': 'bullet' }],
				],

				customToolbar2: [
					['bold', 'italic'],
					[{ 'list': 'ordered'}, { 'list': 'bullet' }],
					['image', 'code-block', 'blockquote'],
					[{ 'header': [3, 4, 5, 6, false] }],
				],

				input: {
					introducao: '',
					'materiais-e-metodos': '',
					resultados: '',
					conclusao: '',
				},

				review: {
					introducao: [],
					'materiais-e-metodos': [],
					resultados: [],
					conclusao: [],
				},

				data: {
					title: '',
					authors: [],
					keywords: [],

					apoio: '',
					referencias: '',

					embasamento: '',
					problematica: '',
					objetivos: '',
					metodologia: '',
					resultados: '',
					conclusao: '',
					contribuicao: '',
				},

				call: {},

				loadingInitGET: false,

				lastSent: {},

				content: '',

				pages: 0,

				subPages: [{
					name: 'Introdução',
					id: 'introduction',

					info: ['Deverá trazer informações que justifiquem o trabalho. Não deverá ser muito longa a ponto de reduzir o espaço dos itens “MATERIAIS E MÉTODOS” e “RESULTADOS E DISCUSSÕES” prejudicando o entendimento do trabalho.', 'As citações dentro do texto deverão ser da seguinte forma: (LIMA, 1995) para um único autor; (VIEIRA & SILVA, 1992) para dois autores; (VIEIRA, SILVA & BORGES, 1995) para três autores; (KINGSTON et al., 2010) para mais de três autores. No texto corrido deverá ser usado o seguinte formato: Lousada (1976) para um único autor; Nogueira & Ramos (1987) para dois autores; Araújo, Nogueira & Ramos (1997) para três autores; Carvalho et al. (2010) para mais de três autores. Somente essas formas poderão ser usadas.', 'Todas as referências citadas no texto deverão constar em “REFERÊNCIAS”. A introdução deve conter também o(s) objetivo(s) do trabalho, de forma clara e sucinta. O último parágrafo da introdução deve conter o seu fechamento.'],
					value: '',
					last: '',
				}, {
					name: 'Problemática',
					id: 'problem',

					info: [],
					value: '',
					last: '',
				}, {
					name: 'Pergunta(s)/Hipótese(s)',
					id: 'hypothesis',

					info: [],
					value: '',
					last: '',
				}, {
					name: 'Objetivos Gerais',
					id: 'general_objectives',

					info: [],
					value: '',
					last: '',
				}, {
					name: 'Objetivos Específicos',
					id: 'specific_objectives',

					info: [],
					value: '',
					last: '',
				}, {
					name: 'Justificativa',
					id: 'justification',

					info: [],
					value: '',
					last: '',
				}, {
					name: 'Materiais e Metódos/Metodologia',
					id: 'materials',

					info: ['Dependendo  da natureza do trabalho, uma caracterização da área experimental deve ser inserida, tornando claras as condições em que a pesquisa foi realizada. Quando os métodos utilizados forem os recomendados, apenas citar a referência bibliográfica conforme instruções apresentadas, caso contrário é necessário descrever sucintamente os procedimentos utilizados, modificações promovidas e etc.'],
					value: '',
					last: '',
				}, {
					name: 'Resultados e Discussões',
					id: 'results',

					info: ['As tabelas e as figuras podem ser inseridas no texto e os resultados devem ser apresentados e discutidos. Havendo necessidade, esse item também pode ser subdividido.'],
					value: '',
					last: '',
				}, {
					name: 'Conclusões ou Considerações Finais',
					id: 'conclusion',

					info: ['Devem ser apresentadas em frases sucintas, sem comentários adicionais, com o verbo no presente do indicativo; não devem ser uma repetição dos resultados e devem responder aos objetivos expressos no trabalho; não podem consistir em um resumo dos resultados; devem apresentar as novas descobertas da pesquisa.'],
					value: '',
					last: '',
				}],
			}
		},

		created () {
			this.getDocument()
		},

		mounted () {
			let self = this.$refs['page-view'].$el

			let father = self.parentNode

			let width = father.clientWidth * .8
			let height = width * 1.414285714285714

			self.style.width = width + 'px'
			self.style.height = height + 'px'

			self.style.fontSize = (1.087 / height * 100) + 'px'
		},

		methods: {
			openModal () {
				$('#exampleModalLong').modal('show');
			},

			closeModal (name) {
				$(`${name}`).modal('hide');
			},

			getDocument () {
				this.loadingInitGET = true
				let self = this

				this.$resource('v2/document/{id}/abstract-expanded').get({ id: this.$route.params.id, }).then(response => {
					self.loadingInitGET = false

					let body = response.body

					if (body.error) {
						alert(body.error)
						return false
					}

					this.subPages.forEach(page => {
						if (typeof body[page.id] == 'string') {
							page.value = body[page.id]
							page.last = body[page.id]
						}
					})

					this.loadedImages = body.images.map(image => {
						image.position = null
						return image
					})
				}, response => {
					self.loading = false
					alert(response.body.message)
				})
			},

			updateDocument (page) {
				let self = this

				self.content = self.updateContent()

				this.$resource('v2/document/{id}/abstract-expanded').update({ id: this.$route.params.id, }, {
					attribute: page.id,
					value: page.value,
				}).then(response => {
					let body = response.body

					if (body.error) {
						alert(body.error)
						return false
					}
				}, response => {
					self.loading = false
					alert(response.body.message)
				})
			},

			removeTagP (string) {
				if (typeof string == 'string')
					return string.replace && string.replace(/<\/?p[^>]*>/g, '')
			},

			updateInTime (page) {
				if (page.value == page.last)
					return false

				let self = this

				if (this.call[page.id]) {
					clearTimeout(this.call[page.id])
				}

				this.call[page.id] = setTimeout(() => {
					self.updateDocument(page)
					page.last = page.value
				}, 500);
			},

			updateContent () {
				let self = this

				let html = '<span style="font-family: \'Times New Roman\'">'

				html += `<span style="
							padding-left: 1.4em;
							font-size: 14em;
							text-align: center;
							font-weight: bold;
							display: block;
							text-transform: uppercase;
							word-wrap: break-word;
						">`
				html += this.data.title
				html += '</span>'


				html += `<span style="
							height: 25em;
							display: block;
						"></span>`


				html += `<span style="
							font-size: 12em;
							text-align: justify;
							font-family: Arial;
							font-weight: bold;
							display: block;
						">`
				// html += '[[SILVA, Maria Aparecida da¹; SILVA, Maria Aparecida da²]]'

				let authors = []

				this.data.authors.forEach((author, index) => {
					let separated = author.name.split(' ')

					let text = ''

					if (index == 0)
						text += '<u>'

					text += separated.slice(-1).join(' ').toUpperCase()
					text += ', '

					let name = separated.slice(0, -1).join(' ').toLowerCase()
					name = name.split(' ').map(word => {
						if (['dos', 'de', 'da'].indexOf(word) != -1)
							return word
						else
							return word.slice(0, 1).toUpperCase() + word.slice(1).toLowerCase()
					}).join(' ')
					text += name

					if (index == 0)
						text += '</u>'

					text += `<sup>${index + 1}</sup>`

					authors.push(text)
				})

				html += authors.join('; ')

				html += '</span>'


				html += `<span style="
							height: 15em;
							display: block;
						"></span>`


				html += `<span style="
							display: block;
							border-bottom: 1px solid black;
						">`
				html += '</span>'


				html += `<span style="
							height: 15em;
							display: block;
						"></span>`



				html += `<span style="
							display: block;
							font-size: 11em;
							word-wrap: break-word;
						">`
					html += `<span style="
								font-weight: bold;
							">`
					html += 'RESUMO: '
					html += '</span>'

				html +=
					this.data.embasamento +
					this.data.problematica +
					this.data.objetivos +
					this.data.metodologia +
					this.data.resultados +
					this.data.conclusao +
					this.data.contribuicao

				html += '</span>'

				html += `<span style="
							height: 11em;
							display: block;
						"></span>`

				html += `<span style="
							display: block;
							font-size: 11em;
						">`
					html += `<span style="
								font-weight: bold;
							">`
					html += 'Palavras-chave: '
					html += '</span>'

				if (this.data.keywords.split) {
					let keywords = this.data.keywords.split(';').filter(word => word.length)

					if (keywords.length > 0)
						html += keywords[0]

					for (var i = 1; i < keywords.length; i++) {
						if (i == keywords.length - 1) {
							html += ' e '
						} else {
							html += ', '
						}

						html += keywords[i]
					}

					html += '.'
				}

				html += '</span>'

				html += `<span style="
							height: 18em;
							display: block;
							word-wrap: break-word;
						"></span>`

				let array = []

				this.subPages.forEach(page => {
					let content = page.value

					content = content.replace(/<p( class="fig"| id="([0-9]+)"| contenteditable="false"| draggable="true")+>\[Fig ([0-9]+)\]<\/p>/g, function ($1, $2, $id, $numFig) {
						let string = ''

						self.loadedImages.forEach(image => {
							if (image.id == $id) {
								console.log(image)
								string = `
									<img src="${self.baseImage}${image.image.name}"></img>
								`

								console.log(string)
							}
						})

						return string
					})

					array.push({
						title: page.name.toUpperCase(),
						content: content,
					})
				})

				if (array.forEach)
					array.forEach(field => {

						html += `<span style="
									display: block;
									font-size: 12em;
									font-weight: bold;
								">`
						html += field.title
						html += '</span>'

						html += `<span style="
									height: 8em;
									display: block;
								"></span>`


						html += `<span style="
									display: block;
									font-size: 12em;
									text-align: justify;
									word-wrap: break-word;
								">`
						html += field.content.replace(/<p>/g, '<p style="text-indent: 3em; margin: 0;">').replace(/<br>/g, '')
						html += '</span>'

/*
						if (field.title == 'RESULTADOS E DISCUSSÃO') {
							html += '</span></p>'
						}*/

						html += `<span style="
									height: 18em;
									display: block;
								"></span>`
					})

				html += `<span style="
							display: block;
							font-size: 12em;
							font-weight: bold;
						">`
				html += 'REFERÊNCIAS'
				html += '</span>'

				html += `<span style="
							height: 8em;
							display: block;
						"></span>`

				html += `<span style="
							display: block;
							font-size: 12em;
							text-align: left;
							word-wrap: break-word;
						">`
				html += this.data.referencias.replace(/<p>/g, '<p style="margin: 1em;">').replace(/<br>/g, '')
				html += '</span>'

				html += '</span>'

				return html
			},

			submit () {
				let self = this

				/*if (!confirm('Confirma a submissão?\nDepois de submeter, não poderá ser mais revisado.'))
					return false;*/

				this.do.call('set-document', {
					id: this.$route.params.id,
					type: 'submit',
					pages: this.pages,
				}, body => {
					if (body.error) {
						if (body.redirect) {
							if (body.redirect == 'confirmations')
								self.$router.push(`/document/${self.$route.params.id}/confirmations`)
						}

						alert(body.error)
					}

					else if (body.success) {
						self.$router.push('/management')
						alert('Sua proposta foi submetida com sucesso!')

					} else
						alert('Algo inesperado aconteceu')
				})
			},

			receivePages (pages) {
				this.pages = pages
			},

			uploadImage (event) {
				let self = this

				let element = event.srcElement

				if (element.files.length == 0)
					return false

				let reader = new FileReader();

				reader.addEventListener("load", function () {
					let url = reader.result

					let base64 = url.slice(url.search('base64,') + 7)

					self.$resource('https://prequest.websiteseguro.com/dev-ifal/back-end/v2/image').save({ id: self.$route.params.id, }, {
						image: base64,
					}).then(response => {
						let body = response.body

						if (body.error) {
							alert(body.error)
							return false
						}

						self.returnOfAddImage = body

						self.addImageInput.title = ''
						self.addImageInput.source = ''
						self.addImageInput.info = ''

						self.typeImageModal = 'add'

						$('#myModal').modal('show')
					}, response => {
						self.loading = false
						alert(response.body.message)
					})
				}, false);

				reader.readAsDataURL(element.files[0]);
			},

			editImage (image) {
				this.returnOfAddImage = image.image
				this.currentImageId = image.id
				this.addImageInput.title = image.title
				this.addImageInput.source = image.source
				this.addImageInput.info = image.info
				this.typeImageModal = 'edit'
				$('#myModal').modal('show')
			},

			onEditImage (image) {
				this.loadedImages = this.loadedImages.map(img => {
					if (img.id == image.id) {
						img.title = image.title
						img.source = image.source
						img.info = image.info
					}

					return img
				})
			},

			onAddImage (image) {
				image.position = null
				this.loadedImages.push(image)

				let p = document.createElement('p')

				let p2 = document.createElement('p')

				p.setAttribute('contenteditable', false)
				p.setAttribute('draggable', true)

				p.appendChild(
					document.createTextNode(`[Fig. 1]`)
				)

				p.setAttribute('class', 'fig')
				p.setAttribute('id', image.id)

				p.addEventListener('dragstart', ondragstart)
				p2.addEventListener('dragenter', ondragenter)

				let father = document.querySelector(`#${this.currentPage} .ql-editor`)

				if (father.appendChild) {
					father.appendChild(p)
					father.appendChild(p2)
				}
			},

			removeImage (image) {
				let self = this

				this.$resource('v2/document/{document}/abstract-expanded/image/{image}').delete({
					document: this.$route.params.id,
					image: image.id,
				}).then(response => {
					self.sendingData = false

					let body = response.body

					if (body.error) {
						alert(body.error)
						return false
					}

					let found = null

					self.loadedImages.forEach(image => {
						if (image.id == body.id)
							found = image
					})

					if (found)
						self.loadedImages.splice(
							self.loadedImages.indexOf(found),
							1
						)
				}, response => {
					self.sendingData = false
					alert(response.body.message)
				})
			},
		},

		watch: {
			'subPages': {
				handler (value) {
					let self = this

					document.querySelectorAll('.ql-editor').forEach(editor => {
						if (!editor.childNodes)
							return false

						editor.childNodes.forEach(paragraph => {
							if (paragraph.tagName && paragraph.tagName == 'P') {
								if (paragraph.classList.contains('fig')) {
									paragraph.addEventListener('dragstart', ondragstart)
								} else {
									paragraph.addEventListener('dragenter', ondragenter)
								}
							}
						})
					})

					let current = 1

					document.querySelectorAll('.ql-editor .fig').forEach((figure, index) => {
						if (!figure.id) {
							figure.classList.remove('fig')
							figure.setAttribute('contenteditable', 'true')
							return false
						}

						figure.textContent = `[Fig ${current}]`

						let found = false

						self.loadedImages.forEach(image => {
							if (image.id == figure.id)
								found = image
						})

						if (found)
							found.position = current
						else {
							found.position = null
							figure.classList.remove('fig')
							figure.setAttribute('contenteditable', 'true')
							return false
						}

						current++
					})

					setTimeout(() => {
						let figs = document.querySelectorAll(`.ql-editor .fig`)

						self.loadedImages.forEach(image => {
							let found = false

							figs.forEach(figure => {
								if (figure.id == image.id)
									found = true
							})

							if (!found)
								self.removeImage(image)
						})
					}, 0)
				},
				deep: true,
			},
		},

		computed: {
			baseImage () {
				if (location.href.slice(0, -location.hash.length).match(/(dev|localhost)/))
					return 'https://prequest.websiteseguro.com/dev-ifal/back-end/v2/public/images/'
				else
					return 'http://localhost/comunica-wuas/back-end/v2/public/images/'
			},
		},
	}

</script>

<style scoped>

	.info-field {
		font-size: 10pt;
		text-align: justify;
		color: red;
	}

	.view {
		position: relative;
		min-height: 510px;
		transition: .2s;
	}

	.view .header {
		background-color: white;
		box-shadow: 0 -5px 10px rgba(0, 0, 0, 0.2) inset;
		padding: .5em .2em;
		top: 0;
		transition: .2s;
	}

	.view .document-content {
		overflow: auto;
		background-color: gray;
	}

	.view.error .document-content {
		background-color: #e4a39d;
	}

	.view.error .header {
		background-color: #e74c3c;
		color: white;
	}

	.view.error .header .error {
		font-size: 0.7em;
	}

	.ql-editor img {
		height: 100px;
		display: block;
	}

	.tool {
		cursor: pointer;
	}

	.tool:hover {
		color: #06c;
	}

</style>

<style type="text/css">

	.ql-editor .fig {
		background-color: lightblue;
		display: block;
		width: 100%;
		text-align: center;
		height: 2em;
		line-height: 2em;
	}

</style>
