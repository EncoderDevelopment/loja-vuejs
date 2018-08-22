# loja-vuejs
Aplicação de loja virtual para edição de produtos de uma loja virtual fictícia


Descreição: Loja virtual,
Aplicação de loja virtual para edição de produtos de uma loja virtual fictícia.

Notas da versão:
 - O projeto foi desenvolvido VueJs;
 - Foram utilizado no desenvolvimento:
	- NodeJs 8.11.14 64bits;
	- Bootstrap versão 3.3.6;
	- vee-validate@next
	- son-server
	- rpm
	

Pré-requisitos:
 - NodeJs, rpm
 - produtos.json, nesse projeto utilizamos como base de dados o arquivo  produtos.json

A aplicação inicia na pagina de listagem de produtos,
nessa página é possivel fazer um CRUD completo de produtos da loja,
contendo:
 - Uma tela de listagem;
 - Uma tela de cadastro / edição;
 - Exclusão de produtos.


Link Novo produto:
 - Acessa a pagina de cadatro de novos produtos;
 - A url da foto do produto é vinculada em um link web,
	Para fins de e economia de tempo optou-se por não,
	implementar um metodo de evio de fotos e pre-processamento de imagens,
	para tal, basta copiar o link de uma imagem do servidor ou da web;

Link vitrine de produtos:
 - Acessa uma pagina onde os produtos são mostrados conforme a vitrine da loja;
 - Essa pagina esta parcialmente finalizada;
 - Apenas visualização de produtos é possivel;


O que esta aplicação não contem:
 - autenticação de usuário;
 - Metodos de validação complexos;
 - Carrinho de compras ou opções de finalização de venda;


