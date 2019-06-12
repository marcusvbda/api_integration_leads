# API DE INTEGRAÇÃO DE LEADS - EZCORE LEADS E ORION ERP

Esta API foi desenvolvida exclusivamente para integração entre os sistemas EZCore Leads e Orion ERP. Trata-se de uma API que permite a transferência de informações de leads, bem como seus históricos, de forma segura e rápida.

Veja como funciona essa integração!

## Como devo usar ?
###  ETAPA 1 - Autenticação de Integração
O início desta integração requer a realização de uma requisição GET, do tipo HTTP, utilizando a rota abaixo:

http://leads.ezcore.com.br/api/unimar_integration/get/auth?client_id={client_id}&secret_key={secret}.

Para esta requisição, os seguintes parâmetros são necessários:
- Client ID
- Secret Key
**Solicite estes dados de integração ao administrador da API, através do email vinicius.bda@icloud.com.*

A resposta da requisição é apresentada em formato JSON, trazendo as seguintes informações:
- **Success**: True, quando as informações foram corretamente autenticadas/ False, quando houver alguma irregularidade na requisição.
- **Client ID**: Com  o client_id informado previamente na requisição GET.
- **Secret Key**: Com a secret_key informado previamente na requisição GET. 
- **Token***: Gerado automaticamente pela API para realizar a requisição das informações dos Leads. 
Veja o exemplo:

```
{
    "success": true,
    "auth": {
        "client_id": "{client_id}",
        "secret_key": "{secret_key}",
        "token": "{token}"
    }
}
```  
**O Token expira a cada duas horas. Portanto, é possível realizar requisições utilizando o mesmo token por até duas horas, ou efetuar quantas requisições forem necessário, utilizando um token para cada requisição, por exemplo.*


### ETAPA 2 - Captura de Leads

 A captura de Leads deve ser realizada por meio de uma nova requisição GET, utilizando a seguinte rota : 
 
 http://leads.ezcore.com.br/api/unimar_integration/get/leads?token={token}&status=sem-agendamento&create_date_start={AAAA-MM-DD}&create_date_end={AAAA-MM-DD}&update_date_start={AAAA-MM-DD}&update_date_end={AAAA-MM-DD}&tags={tag1,tag2,tag3}&ref_token={ref_token}&page=1
 
##### Os Parâmetros
 Todos os parâmetros desta requisição são separados por um &. São eles: 
- **token***: Deve ser preenchido com o token autenticado, gerado automaticamente pela API na etapa 1 desta documentação.
- **status**: Refere-se ao status do lead captado. As opções de status são:"sem-agendamento", "agendado","cancelado","finalizado" e "vestibular". Caso este parâmetro seja preenchido incorretamente, a API retornará a seguinte resposta:
 ```
 {
    "success": false,
    "message": "status not found,check the status options",
    "status_options": [
        "sem-agendamento",
        "agendado",
        "cancelado",
        "finalizado",
        "vestibular"
    ]
}
```  
- **create_date_start e create_date_end**: Referem-se a data de criação do Lead. São utilizados para filtrar um período específico. Se preenchidos, estes parâmetros devem obedecer o formato AAAA-MM-DD.
- **update_date_start e update_date_end**: Referem-se a data de última atualização do Lead. São utilizados para filtrar um período específico. Se preenchidos, estes parâmetros devem obedecer o formato AAAA-MM-DD.
- **tags**: Refere-se as tags preenchidas no Lead. Ė utilizada para filtrar leads que possuem tags específicas. Se preenchido, este parâmetro deve separar as tags entre vírgulas. 
- **ref_token**: Trata-se de um código único de identificação, utilizado pelos sistemas integrados.
- **page**: Refere-se ao número da página atual. A  API realiza a paginação dos leads, retornando vinte itens por página. Desse modo, caso a requisição apresente mais que vinte leads, estes serão relacionados em várias páginas. 
Por este motivo, o JSON de resposta desta requisição sempre informará o número da página atual, através do parâmetro *current_page*, e o total de páginas existentes para a solicitação realizada, representado pelo parâmetro *total*.
Assim, para acessar os demais leads da solicitação, é necessário realizar uma requisição para cada página existente.
Veja um exemplo de resposta de requisição desta etapa:

```
{
  "success": true,
  "leads": {
    "current_page": 1,
    "data": [
      {
        "id": 1,
        "lead_id": 1,
        "status_id": 1,
        "tenant_id": 1,
        "observacoes": "via RD Station",
        "data": null,
        "hora": null,
        "log": [
          {
            "data": "05/06/2019",
            "hora": "10:27:56",
            "texto": "Converteu no evento <b>home-ead</b> no <b>RD Station</b>",
            "responsavel": "RD Station"
          }
        ],
        "resposta_id": 1,
        "objecao_id": 1,
        "outra_objecao": null,
        "importacao_id": null,
        "tags": [
          "teste",
          "teste 2"
        ],
        "ref_token": "560583538",
        "created_at": "2019-06-05 16:34:49",
        "updated_at": "2019-06-05 16:34:49",
        "lead": {
          "id": 1,
          "tenant_id": 1,
          "nome": "Teste - Victor para Vinicius EZCoreE",
          "email": "victorgaldi.ead@unimar.br",
          "telefone": "(14)99899-0773",
          "telefone_2": "",
          "cidade": "",
          "curso": "ANÁLISE E DESENV. SISTEMAS",
          "observacoes": "via RD Station",
          "mensagem": null,
          "importacao_id": null,
          "created_at": "2019-06-05 16:34:49",
          "updated_at": "2019-06-05 16:34:49"
        },
        "historico_emails": [],
        "historico_sms": [],
        "status": {
          "id": 1,
          "name": "Sem agendamento",
          "description": null,
          "value": "I",
          "slug": "sem-agendamento",
          "for": "AGENDAMENTO",
          "data": null,
          "created_at": "2019-05-02 08:56:41",
          "updated_at": "2019-05-02 08:56:41"
        },
        "objecao": null,
        "resposta": null,
        "tenant": {
          "id": 1,
          "cnpj": "00.000.000/0000-00",
          "nome": "Polo Marilia",
          "principal": 0,
          "razao": "Universidade de Marilia",
          "cidade": "Marilia",
          "created_at": "2019-05-02 08:07:04",
          "updated_at": "2019-06-10 16:54:16"
        }
      }
    ],
    "first_page_url": "http://leads.ezcore.com.br/api/unimar_integration/get/leads?page=1",
    "from": 1,
    "last_page": 1,
    "last_page_url": "http://leads.ezcore.com.br/api/unimar_integration/get/leads?page=1",
    "next_page_url": null,
    "path": "http://leads.ezcore.com.br/api/unimar_integration/get/leads",
    "per_page": 20,
    "prev_page_url": null,
    "to": 1,
    "total": 2
  }
}
```
**Para esta etapa, somente o parâmetro token é OBRIGATOÓRIO, os demais são opcionais e, se preenchidos, retornam uma busca mais refinada.*


### Informações Complementares

- Todas as requisições desta API devem utilizar o método GET.
- Todos os responses desta API são disponibilizados em formato JSON.

#####  Em caso de dúvidas, sugestões ou correções, contate o desenvolvedor através do email vinicius.bda@icloud.com.br ou telefone (14)99676-6177.
