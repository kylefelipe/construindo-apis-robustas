# 🏆 Desafios

![](imgs/desafios.jpg)

À partir de agora, o seu desafio é continuar escrevendo as funcionalidades que faltam, mas vou te dar umas dicas.

Relembrando, ainda temos as seguintes tarefas a serem feitas.

- [x] Deve apresentar uma interface que possa ser consumida tanto por um website, quanto por um aplicativo para dispositivos móveis;

- [x] Deve prover um _endpoint_ que indique a saúde do sistema;

- [x] Dado um pedido, retornar os seus itens;

- [x] Os itens de um pedido devem conter um identificador (sku), uma descrição, uma imagem, uma referência e a quantidade.

- [ ] Exibir um relatório com o total de métodos de pagamento utilizados nos últimos 30 pedidos;

- [ ] Dado um pedido (vários itens), enriquecer a informação com as informações de gtin (global trade item number). Deve ser retornado também a marca, descrição e identificação do produto.

- [x] Como será consumido por terceiros deve apresentar boa documentação;

- [x] O sistema deve estar preparado para receber novas funcionalidades, garantindo qualidade a cada entrega;

- [x] O sistema deve apresentar testes.


!!! warning
    Como as APIs abertas do Magalu se encontram em alpha, uma autorização prévia é necessária. Por isso, você pode utilizar uma versão simulada da mesma.

    Se não lembra os endpoints disponíveis, volte na [página](./explorando_api.md) onde exploramos a API.


## 📊 Relatório

Exibir um relatório com o total de métodos de pagamento utilizados nos últimos 30 pedidos (em um cenário real este critério seria outro como uma data).

Para obter esta informação precisamos de consultar a [api de pedidos do Magalu](https://alpha.dev.magalu.com/apis/maestro).

Verifique o _endpoint_ que contém esta informação e se atente à paginação do recurso, ou seja, a página só contem 10 pedidos.

Nosso _endpoint_ será `/report/` e o seu formato de resposta será um JSON.

O código de status será `200 OK`.

> Vamos utilizar o mesmo `X-Tenant-id` que já utilizamos para acessar a API de pedidos. Este valor seria dinâmico em uma aplicação real.

```json
[
  {
    "payment_method": "magalu_card",
    "total": 5
  },
  {
    "payment_method": "pix",
    "total": 3
  },
  {
    "payment_method": "mastercard",
    "total": 5
  }
]
```

## 🎁 O produto

Dado uma lista de itens de um pedido, enriquecer com as informações de gtin (global trade item number). Deve ser retornado também o nome, descrição e identificação do produto.

Nosso endpoint será `/orders/enriched`. Diferente do que vimos até agora será uma chamada utilizando o verbo `POST` com o seguinte payload:

```json
{
  "items": [
    "123456789",
    "987654321",
    "444455558"
  ]
}
```

Cada valor contido no array `items` será um código que identifica um produto.

O retorno deve ser um JSON com o seguinte formato:

```json
{
  "items": {
    "123456789": {
        "name": "Produto 1",
        "description": "Descrição do produto 1",
        "gtin": "7890000000000"
    },
    "987654321": {
        "name": "Produto 2",
        "description": "Descrição do produto 2",
        "gtin": "7890000000001"
    }
  },
  "missing": [
    "444455558"
  ]
}
```
Caso algum produto não seja encontrado, deve ser adicionado a chave `"missing"`. 


## 🏁 Concluindo (ou quase)

Assim finalizamos a primeira parte do guia, espero que tenha curtido bastante esta jornada de aprendizado. Daqui pra frente vamos ver técnicas complementares para melhorar a qualidade e robustez do nosso sistema.

Ainda temos várias coisas não abordadas neste guia que complementam nossa aplicação, mas que tornariam a didática pior ou seriam muito extensas. Recomendo que vejam até o final as referências e continuem se aperfeiçoando.

Caso tenham gostado, não deixe de estrelar o repositório como forma de gratidão. Isto motiva a escrever mais materiais interessantes como este em português.

!!! quote "🐂" 
    Uma api robusta possui as funcionalidades esperadas.

