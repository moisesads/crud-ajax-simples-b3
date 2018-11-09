# CRUD Ajax Simples e Reutilizável

- Bootstrap 3
- VueJs 2

<p align="center">
  <img src="https://i.imgur.com/Rq7pqhh.gif" width="700" alt="vform">
</p>

```
npm install crud-ajax-simples
```

Tag HTML

```html

<crud-ajax-simples :fields="fieldsCateg"
                            name="Categoria"
                            url-get-all="api/teste-categs-json"
                            url-post="api/teste-categs-json"
                            url-put="api/teste-categs-json"
                            url-delete="api/teste-categs-json"/>

```
Na prop **fields** é possível configurar os campos da tabela e do formulário de
cadastro de edição.

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
