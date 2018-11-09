# CRUD Ajax Simples e Reutilizável

- Bootstrap 3
- VueJs 2

<p align="center">
  <img src="https://i.imgur.com/Rq7pqhh.gif" width="700" alt="vform">
</p>

# Instalação

```
npm i -s crud-ajax-simples

```
# Tag HTML

```html

<crud-ajax-simples :fields="fieldsCateg"
                    name="Categoria"
                    url-get-all="api/sua-url-get-todos-registros"
                    url-post="api/sua-url-post-cadastro-novo-registo"
                    url-put="api/sua-url-put-registo-id"
                    url-delete="api/sua-url-delete-retistro-id"/>

```
Obs: Basta setar as urls, os IDs são setados automaticamente pelo componente
no momento da realização das operações.

Na prop **fields** é possível configurar os campos da tabela e do formulário de
cadastro de edição, estes devem ser manipulados na sua api.

```js

data(){
  return{
      fieldsCateg: {
            nome: {
                label: 'Nome' ,
                sortable: true,
                form: true,
                type: 'text'
            },
            descricao: {
                label: 'Descrição',
                sortable: true,
                form: true,
                type: 'textarea'
            },
            acoes: {
                label: ''
            }
      },
  }
}

```
