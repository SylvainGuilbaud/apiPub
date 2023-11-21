<p style="text-align: center;"><b>IRIS - apiPub</b></p>
<p style="text-align:center;"><img src="docResources/apiPub.png" alt="wizard" width="250"/></p>

## Introdução
 
Estamos na era da **economia multi-plataforma**, e as API's são a *"liga"* deste cenário digital. Sendo tão importantes, elas são encaradas por desenvolvedores como um **serviço** ou **produto** a ser consumido. Assim sendo, a **experiência** na sua utilização é um fator crucial de **sucesso**.

Visando **melhorar esta experiência**, padrões de especificação, como o [OpenAPI Specification (OAS)](https://swagger.io/specification/#:~:text=Introduction,or%20through%20network%20traffic%20inspection.) estão cada vez mais sendo adotados no desenvolvimento de API's RESTFul.

# O que é o IRIS apiPub?

IRIS ApiPub é um projeto [Open Source](https://en.wikipedia.org/wiki/Open_source), de iniciativa pessoal, que tem como principal objetivo **publicar** automaticamente **API's RESTful** criadas com a tecnologia [Intersystems IRIS](https://www.intersystems.com/try-intersystems-iris-for-free/), da forma mais simples e rápida possível, utilizando o padrão [Open API Specification](https://swagger.io/specification/) (OAS) versão 3.0.  

IRIS apiPub se utiliza da especificação de um método IRIS, e o publica automaticamente como uma API Rest, permitindo a customização para deixá-la o mais amigável possível para o usuário final.

Ele permite que o usuário foque principalmente na **implementação** das API’s (Web Methods), automatizando os demais aspectos relacionados a **documentação, exposição, execução e monitoramento** dos serviços.

Este projeto também inclui uma implementação de exemplo completa ([apiPub.samples.api](/src/apiPub/samples/api.cls)) do *sample* [Swagger Petstore](https://app.swaggerhub.com/apis/Colon-Org/Swagger-PetStore-3.0/1.1), utilizado como *sample* oficial do [swagger](https://swagger.io/). 

![](docResources/PetStore.gif)
### Vídeo de Demonstração:
[![Fast Api Development](https://img.youtube.com/vi/IdJ1PqmhH3c/0.jpg)](http://www.youtube.com/watch?v=IdJ1PqmhH3c "Fast Api Development")
## Como um método IRIS é publicado como uma API Rest:

A tabela a seguir mostra como todos os tipos de parâmetros de método são mapeados e projetados para uma API Rest:

![](docResources/projectionMap.png)
## Instalação usando o [zpm](https://openexchange.intersystems.com/package/ObjectScript-Package-Manager)
Importante: É obrigatória a escolha do namespace que se deseja utilizar antes da instalação. 
```
zn "{namespace}"
```
```
zpm "install iris-apipub"
```

Para testar com o swagger, você deverá instalar a aplicação **swagger-ui**.
```
zpm "install swagger-ui"
```

Você também pode habilitar o [monitoramento](https://github.com/devecchijr/apiPub/blob/master/README-BR.md#habilite-o-monitoramento-opcional).

## Pre-requisitos para a instalação com o Docker
Verifique se você já possui o [git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) e o [Docker desktop](https://www.docker.com/products/docker-desktop) instalados.

## Instalação usando o Docker

1. Faça um *Clone/git pull* do repositório no diretório local.

```
$ git clone https://github.com/devecchijr/apiPub.git
```

2. Abra o terminal neste diretorio e execute o seguinte comando:

```
$ docker-compose build
```

3. Execute o container IRIS com o projeto:

```
$ docker-compose up -d
```
# Agora é possível utilizar o apiPub Wizard para automatizar a publicação de Api's

Com as novas opções abaixo, é possível agilizar a publicação de Apis:

- Novas APIs baseadas em templates. É um excelente ponto de partida porque abrevia todo processo de criação manual (3 passos), incluindo a automação da criação da aplicação Web.
 
- Baseada em uma amostra JSON (sample) - Ele automaticamente cria o schema (OAS) do objeto amostra e o relaciona à Api para validação (parsing).

- Baseada em uma Produção IRIS (interop.). É possível escolher componentes da Produção que serão expostos em Apis rest/json.

- Api First Approach (Introspection) - Ler uma especificação OAS 3.0 e criar o Spec dos métodos para a implementação, assim como na classe [apiPub.samples.apiFirst](/src/apiPub/samples/apiFirst.cls)
  
Se você já instalou as aplicações **apiPub** e **swagger-ui**, você pode abrir a seguinte URL:

http://localhost:52773/swagger-ui/index.html?url=http://localhost:52773/apiPub/wizard/IRISAPP/_spec

**ou**

```
http://{host:port}/swagger-ui/index.html?url=http://{host:port}/apiPub/wizard/oas/{namespace}/_spec
```
![](docResources/wizard.png) 

**ou**

## **Publique sua API no padrão OAS 3.0 em apenas 3 passos:**

## Passo 1  
Defina a classe de implementação das tuas API’s e **rotule** os métodos com o atributo [WebMethod]. Se preferir, utilize a classe ([apiPub.samples.api](/src/apiPub/samples/api.cls)) (PetStore sample).
![](docResources/labelingImplementationMethod.gif)
*Você pode testar os seus métodos SOAP já implementados.*

## Passo 2
Crie uma **subclasse** de apiPub.core.service, conforme exemplo a seguir (copiar/colar) e aponte o parâmetro DispatchClass para a sua classe de Implementação criada anteriormente. Informe também o path de documentação OAS 3.0 (parâmetro OASPath). 
```
Class package.name.module Extends apiPub.core.service
{

/// Charset for Output
Parameter CHARSET = "UTF-8";

/// Target path for OAS Documentation
Parameter OASPath As %String = "Get:/_spec";

/// Target class that contains all API (Web Methods) for this module
Parameter DispatchClass As %String = "package.name.api";

/// When the class is also a Business Service
Parameter DispatchBSName;

/// Use IRIS Production to generate tracing and monitoring (dashboards)
Parameter Traceable As %Boolean = 0;

/// Group for monitoring
Parameter APIDomain = "Api Domain";

/// If OASNestedMode=1 publisher avoid name conflict for OAS Schemas (Complex Requests and Responses)
Parameter OASNestedMode = 0;

/// format/prettify the JSON request body
Parameter BeautifyJsonBodyRequest = 0;

/// format/prettify the JSON response body
Parameter BeautifyJsonBodyResponse = 0;

}
```
![](docResources/configuringServiceClass.gif)

## Passo 3
Crie uma Aplicação Web e aponte a classe de Dispatch para a classe de serviço criada anteriomente.
![](docResources/creatingWebApp.gif)

## Utilize o Swagger
Com o [iris-web-swagger-ui](https://openexchange.intersystems.com/package/iris-web-swagger-ui) é possível expor a especificação do teu serviço. Basta apontar para o path de documentação e ... **VOILÁ!!**  
![](docResources/testingFirstMethod.gif)

Para acessar a página do swagger-ui com a especificação Open Api (OAS) de uma só vez use o exemplo a seguir (apenas mude o parâmetro url para a sua URL open api (OAS)):
```
http://localhost:52773/swagger-ui/index.html?url=http://localhost:52773/crud/_spec
```

## Faça um teste com os teus serviços web existentes 
Se você já possui métodos web publicados (WebServices), você pode testar a sua publicação com Rest/JSON com OAS 3.0.
![](docResources/soapToOASRest.png)

Ao publicar métodos com tipos complexos com a documentação completa do payload em OAS (Open Api Documentation) é necessário que a classe do objeto seja uma subclasse de %JSON.Adaptor ou %XML.Adaptor. Desta maneira métodos web (Ex: SOAP) já construídos se tornam automaticamente compatíveis. Também é possivel usar objetos dinâmicos (%DynamicObjects) e vinculá-lo ao seu respectivo OAS Schema para fins de documentação e validação.

![](docResources/XMLAdaptorCompat.png)

## Monitore as tuas API's com o IRIS Analytics 
Habilite o monitoramento das API's para **administrar** e **rastrear** todas as *chamadas Rest*. Monte também os seus próprios indicadores.

![](docResources/monitorYourAPI.gif)

## Testando a Aplicação
Abra a URL do swagger http://localhost:52773/swagger-ui/index.html

Tente executar alguma API do Petstore, como fazer o *post* de um novo *pet*.

Veja o [dashboard do apiPub Monitor](http://localhost:52773/csp/irisapp/_DeepSee.UserPortal.DashboardViewer.zen?DASHBOARD=apiPub_Monitor_Dashboard/apiPub%20Monitor.dashboard). Tente fazer um drill down no domínio petStore para explorar e analisar as mensagens.

![](docResources/monitorYourAPI.gif)

Mude ou crie metódos na classe [apiPub.samples.api](/src/apiPub/samples/api.cls) e volte a consultar a documentação gerada. 
Repare que todas as mudanças são automaticamentes refletidas na documentação OAS ou nos schemas.

## Defina o cabeçalho da especificação OAS
![](docResources/OASheader.png)

Há duas maneiras de definir o cabeçalho OAS 3.0: 

A primeira é através da criação de um bloco JSON XDATA nomeado como *apiPub* na classe de implementação. Este método permite que se tenha mais de uma Tag e a modelagem é compatível com o padrão OAS 3.0. As propriedades permitidas para customização são *info, tags* e *servers*.
```
XData apiPub [ MimeType = application/json ]
{
    {
        "info" : {
            "description" : "This is a sample Petstore server.  You can find\nout more about Swagger at\n[http://swagger.io](http://swagger.io) or on\n[irc.freenode.net, #swagger](http://swagger.io/irc/).\n",
            "version" : "1.0.0",
            "title" : "IRIS Petstore (Dev First)",
            "termsOfService" : "http://swagger.io/terms/",
            "contact" : {
            "email" : "apiteam@swagger.io"
            },
            "license" : {
            "name" : "Apache 2.0",
            "url" : "http://www.apache.org/licenses/LICENSE-2.0.html"
            }
        },
        "tags" : [ {
            "name" : "pet",
            "description" : "Everything about your Pets",
            "externalDocs" : {
            "description" : "Find out more",
            "url" : "http://swagger.io"
            }
        }, {
            "name" : "store",
            "description" : "Access to Petstore orders"
        }, {
            "name" : "user",
            "description" : "Operations about user",
            "externalDocs" : {
            "description" : "Find out more about our store",
            "url" : "http://swagger.io"
            }
        } ]
    }
}
```
A segunda maneira é através da definição de parâmetros na classe de implementação, assim como no exemplo a seguir:

```
Parameter SERVICENAME = "My Service";

Parameter SERVICEURL = "http://localhost:52776/apipub";

Parameter TITLE As %String = "REST to SOAP APIs";

Parameter DESCRIPTION As %String = "APIs to Proxy SOAP Web Services via REST";

Parameter TERMSOFSERVICE As %String = "http://www.intersystems.com/terms-of-service/";

Parameter CONTACTNAME As %String = "John Doe";

Parameter CONTACTURL As %String = "https://www.intersystems.com/who-we-are/contact-us/";

Parameter CONTACTEMAIL As %String = "support@intersystems.com";

Parameter LICENSENAME As %String = "Copyright InterSystems Corporation, all rights reserved.";

Parameter LICENSEURL As %String = "http://docs.intersystems.com/latest/csp/docbook/copyright.pdf";

Parameter VERSION As %String = "1.0.0";

Parameter TAGNAME As %String = "Services";

Parameter TAGDESCRIPTION As %String = "Legacy Services";

Parameter TAGDOCSDESCRIPTION As %String = "Find out more";

Parameter TAGDOCSURL As %String = "http://intersystems.com";
```
## Customize as tuas API's
![](docResources/customizeYourAPI.png)

É possível customizar vários aspectos das API's, como ***tags, paths e verbos***. Para tal, é necessária a utilização de uma notação específica, definida no comentário do método a ser customizado. 

Sintaxe:
>***/// @apiPub[assignment clause]***  
[*Method/ClassMethod*] *methodName(params as type) As responseType* {  
>    
>}

Todas as customizações dadas como exemplo nesta documentação estão disponíveis na classe [apiPub.samples.api](/src/apiPub/samples/api.cls).

## Customizando os Verbos
Quando não há nenhum tipo complexo como parâmetro de entrada, apiPub atribui automaticamente o verbo como *Get*. Caso contrário é atribuído o verbo *Post*. 

Caso se queira customizar o método adiciona-se a seguinte linha nos comentários do método.

>/// @apiPub[verb="*verb*"]

Onde *verb* pode ser **get, post, put, delete ou patch**. 

Exemplo:

>/// @apiPub[verb="put"]

## Customizando os Caminhos (Paths)
Esta ferramenta atribui automaticamente os *paths* ou o roteamento para os *Web Methods*. Ele utiliza como padrão o nome do método como *path*.

Caso se queira customizar o **path** adiciona-se a seguinte linha nos comentários do método.

>/// @apiPub[path="*path*"]

Onde *path* pode ser qualquer valor precedido com barra, desde que não conflita com outro *path* na mesma classe de implementação. 

Exemplo:
>/// @apiPub[path="/pet"]

Outro uso bastante comum do path é definir um ou mais parâmetros no próprio path. Para tal, é necessário que o nome do parâmetro definido no método esteja entre chaves. 

Exemplo:
>/// @apiPub[path="/pet/{petId}"]  
Method getPetById(petId As %Integer) As apiPub.samples.Pet [ WebMethod ]   
{  
}

Quando o nome do parâmetro interno difere do nome do parâmetro exposto, pode-se equalizar o nome conforme exemplo a seguir:
>/// @apiPub[path="/pet/{petId}"]  
/// @apiPub[params.pId.name="petId"]  
Method getPetById(pId As %Integer) As apiPub.samples.Pet [ WebMethod ]   
{  
}

No exemplo acima, o parâmetro interno *pId* é exposto como *petId*.

## Customizando as Tags

É possível definir a **tag**(agrupamento) do método quando há mais que uma tag definida no cabeçalho.
>/// @apiPub[tag="*value*"]

Exemplo:
>/// @apiPub[tag="user"]

## Customizando o Sumário e a Descrição
apiPub automaticamente define o sumário como a descrição do método (sem notação @apiPub), mas é possível alterar.

>/// @apiPub[summary="*content*"]

A descrição também pode ser definida.

>/// @apiPub[description="*content*"]

## Customizando o *Status Code* de Sucesso

Caso se queira alterar o *Status Code* sucesso do método, que é por padrão ***200***, utiliza-se a seguinte notação.
>/// @apiPub[successfulCode="*code*"]

Exemplo:
>/// @apiPub[successfulCode="201"]

## Customizando *Status Codes* de Exceção

Esta ferramenta assume como padrão o ***Status Code 500*** para quaisquer exceções. Caso se queira adicionar novos códigos para exceção na documentação, utiliza-se a seguinte notação.
>/// @apiPub[statusCodes=[{code:"*code*",description:"*description*"}]]

Onde a propriedade *statusCodes* é um array de objetos com código e descrição. 

Exemplo:
> /// @apiPub[statusCodes=[  
    ///     {"code":"400","description":"Invalid ID supplied"}  
    ///     ,{"code":"404","description":"Pet not found"}]  
    /// ]

Ao disparar a exceção, Inclua o *Status Code* na descrição da exceção entre os sinais de "<" e ">".

Exemplo:
> Throw ##Class(%Exception.StatusException).CreateFromStatus($$$ERROR($$$GeneralError, "***<400>*** Invalid ID supplied"))}

Veja o método ***getPetById*** da classe [apiPub.samples.api](/src/apiPub/samples/api.cls)

## Marcando a API como Descontinuada

Para que a API seja exposta como ***deprecated***, utiliza-se a seguinte notação:

>/// @apiPub[deprecated="true"]

## Customizando o *operationId*

Segundo a especificação OAS, ***operationId*** é uma string única usada para identificar uma API ou operação. Nesta ferramenta ela é utilizada para a mesma finalidade no [monitoramento e rastreamento](https://github.com/devecchijr/apiPub#monitore-a-chamada-das-suas-apis-com-o-iris-analytics) das operações.

Por padrão, ela recebe o mesmo nome do método da classe de implementação.

Caso se queira alterá-la utiliza-se a seguinte notação 

>/// @apiPub[operationId="updatePetWithForm"]

## Alterando o charset do método

O charset padrão da geralmente é definido através do parâmetro CHARSET na classe de serviço, descrita no [Passo 2](https://github.com/devecchijr/apiPub#passo-2). Caso se queira customizar o charset de um método, deve se utilizar a seguinte notação:

>/// @apiPub[charset="*value*"]

Exemplo:

>/// @apiPub[charset="UTF-8"]

## Alterando o Tracing de um método individualmente

Mesmo que o parâmetro *traceable* seja definido em seu módulo (classe herdada apiPub.core.service), é possível mudar o comportamento de rastreabilidade individualmente usando a seguinte notação:

>/// @apiPub[trace="true/false"]

## Customizando nomes e outras funcionalidades dos parâmetros
Pode-se customizar vários aspectos de cada parâmetro de entrada e saída dos métodos, como por exemplo os nomes e as descrições que serão expostas para cada parâmetro.

Para se customizar um parametro específico utiliza-se a seguinte notação

>/// @apiPub[params.*paramId.property*="*value*"]

ou para respostas:

>/// @apiPub[response.*property*="*value*"]

Exemplo:
>/// @apiPub[params.pId.name="petId"]  
/// @apiPub[params.pId.description="ID of pet to return"]

Neste caso, está sendo atribuido o nome *petId* e a descrição *ID of pet to return* para o parâmetro definido como *pId*

## Outras Propriedades que podem ser customizadas para parâmetros específicos

Utilize a seguinte notação para parâmetros de entrada ou saída:
>/// @apiPub[params.*paramId.property*="*value*"]

Para respostas:
>/// @apiPub[response.*property*="*value*"]

|   Property           |   Description                                                                                                                                                                                                                                                                                                                                        |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|   description           |   descrição detalhada do parâmetro ou resposta                                                                                                                                                                                                                                        |
|   name           |   se o parâmetro ou resposta precisa ser exposto com um nome diferente                                                                                                                                                                                                                                         |
|   schema           |   nome do schema relacionado dentro do bloco XData (schemas) para parametros ou respostas (response)                                                                                                                                                                                                                                       |
|   parse           |   "false" para não fazer o parsing quando há um schema relacionado.                                                                                                                                                                                                                                         |
|   required           |   "true" se o parâmetro for requerido. Todos os parâmetros do tipo **path** já são automaticamente requeridos                                                                                                                                                                                                                                        |
|   schema.items.enum  |   exposição de Enumeradores para tipos %String ou %Library.DynamicArray. Veja o método ***findByStatus*** da classe [apiPub.samples.api](/src/apiPub/samples/api.cls)                                                                                                                                                                                           |
|   schema.default     |   aponta para um valor default para enumeradores.                                                                                                                                                                                                                                                                                                    |
|   inputType          |   por padrão é **query parameter** para os tipos simples e **application/json** para os tipos complexo (body). Caso se queira alterar o tipo de input, pode se utilizar este parâmetro. Exemplo de uso: Upload de uma imagem, que normalmente não é do tipo JSON. Veja método ***uploadImage*** da classe [apiPub.samples.api](/src/apiPub/samples/api.cls).    |
|   outputType         |   por padrão é **header** para os tipos %Status e **application/json** para o restante. Caso se queira alterar o tipo de output, pode se utilizar este parâmetro. Exemplo de uso: Retorno de um token ("text/plain"). Veja método ***loginUser*** da classe [apiPub.samples.api](/src/apiPub/samples/api.cls)                                                   |

## Relacione Schemas Parseáveis a tipos JSON Dinâmicos ***(%Library.DynamicObject)***
![](docResources/parsingDynamicTypes.gif)
É possível relacionar [schemas OAS 3.0](https://swagger.io/docs/specification/data-models/) a [tipos dinâmicos](https://docs.intersystems.com/hs20201/csp/docbook/DocBook.UI.Page.cls?KEY=GJSON_create) internos. 

A vantagem de se relacionar o schema com o parâmetro, além de informar ao usuário a especificação do objeto requerido, é o ***parsing automático*** do request é realizado na chamada da API. Se o usuário da API por exemplo enviar uma propriedade que não está no schema, enviar uma data em um formato inválido ou não enviar uma propriedade obrigatória, um ou mais erros serão retornados ao usuário informando as irregularidades.

O primeiro passo é incluir o schema desejado no bloco XDATA conforme exemplo abaixo. Neste caso o schema chamado *User* pode ser utilizado por qualquer método. Ele deve seguir as mesmas regras da modelagem [OAS 3.0](https://swagger.io/docs/specification/data-models/). 
```
XData apiPub [ MimeType = application/json ]
{
    {
        "schemas": {
            "User": {
                "type": "object",
                "required": [
                    "id"
                ],
                "properties": {
                    "id": {
                        "type": "integer",
                        "format": "int64"
                    },
                    "username": {
                        "type": "string"
                    },
                    "firstName": {
                        "type": "string"
                    },
                    "lastName": {
                        "type": "string"
                    },
                    "email": {
                        "type": "string"
                    },
                    "password": {
                        "type": "string"
                    },
                    "phone": {
                        "type": "string"
                    },
                    "userStatus": {
                        "type": "integer",
                        "description": "(short) User Status"
                    }
                }
            }            
        }
    }
}
```

O segundo passo é relacionar o nome do schema informado no passo anterior ao parâmetro interno do tipo [%Library.DynamicObject](https://docs.intersystems.com/hs20201/csp/docbook/DocBook.UI.Page.cls?KEY=GJSON_create) usando a seguinte notação:

>/// @apiPub[params.*paramId*.*schema*="*schema name*"]

Exemplo associando o parâmetro *user* ao schema *User*:
```
/// @apiPub[params.user.schema="User"]
Method updateUserUsingOASSchema(username As %String, user As %Library.DynamicObject) As %Status [ WebMethod ]
{
    code...
}
```

Exemplo de request com erro a ser submetido. A propriedade username2 não existe no schema *User*. A propriedade id também não foi especificada e é requerida:
```
{
  "username2": "devecchijr",
  "firstName": "claudio",
  "lastName": "devecchi junior",
  "email": "devecchijr@gmail.com",
  "password": "string",
  "phone": "string",
  "userStatus": 0
}
```

Exemplo de erro retornado:
```
{
  "statusCode": 0,
  "message": "ERRO #5001: <Bad Request> Path User.id is required; Invalid path: User.username2",
  "errorCode": 5001
}
```

Veja métodos ***updateUserUsingOASSchema*** e ***getInventory*** da classe [apiPub.samples.api](/src/apiPub/samples/api.cls). O método ***getInventory*** é um exemplo de schema associado à saída do método (response), portanto não é parseável.

### Gere o schema OAS 3.0 com base em um objeto JSON

Para auxiliar na geração do schema OAS 3.0, você pode usar o seguinte recurso:

**Defina** uma variável com uma amostra do objeto JSON.
```
set myObject = {"prop1":"2020-10-15","prop2":true, "prop3":555.55, "prop4":["banana","orange","apple"]}
```

**Utilize o método utilitário** da classe [apiPub.core.publisher](/src/apiPub/core/publisher.cls) para gerar o schema:
```
do ##class(apiPub.core.publisher).TemplateToOpenApiSchema(myObject,"objectName",.schema)
```

**Copie e cole** o schema retornado no bloco XDATA:

Exemplo:

```
XData apiPub [ MimeType = application/json ]
{
    {
        "schemas": {
            {
                "objectName":   
                {
                    "type":"object",
                    "properties":{
                        "prop1":{
                            "type":"string",
                            "format":"date",
                            "example":"2020-10-15"      
                        },
                        "prop2":{
                            "type":"boolean",
                            "example":true
                        },
                        "prop3":{
                            "type":"number",
                            "example":555.55
                        },
                        "prop4":{
                            "type":"array",
                            "items":{
                                "type":"string",
                                "example":"apple"
                            }
                        }
                    }
                }
            }
        }
    }
}
```

## Habilite o Monitoramento *(Opcional)*

1 - Adicione e ative os seguintes componentes na tua *Production* (*IRIS Interoperability*)

|   Component          |    Type           |  
|----------------------|-------------------|  
|  apiPub.tracer.bm    |  Service (BS)     |
|  apiPub.tracer.bs    |  Service (BS)     |
|  apiPub.tracer.bo    |  Operation (BO)   |

2 - Ative o monitoramento na classe descrita no [Passo 2](https://github.com/devecchijr/apiPub#passo-2)

O parâmetro ***Traceable*** deve estar ativado.
```
Parameter Traceable As %Boolean = 1;

Parameter TracerBSName = "apiPub.tracer.bs";

Parameter APIDomain = "samples";
```
O parâmetro ***APIDomain*** é utilizado para agrupar as API's no monitoramento.

3 - Importe os dashboards
```
zn "IRISAPP" 
Set sc = ##class(%DeepSee.UserLibrary.Utils).%ProcessContainer("apiPub.tracer.dashboards",1)
```

Outros dashboards também podem ser criados com base no cubo ***apiPub Monitor***.

## Utilize esta ferramenta em conjunto com o Intersystems API Manager
Roteie as suas API's geradas e obtenha diversas vantagens com o [Intersystems API Manager](https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=AFL_IAM)

## Compatibilidade

[ApiPub](https://github.com/devecchijr/apiPub#iris-apipub) é compatível com o produto [Intersystems IRIS](https://www.intersystems.com/products/intersystems-iris/) ou [Intersystems IRIS for Health](https://www.intersystems.com/products/intersystems-iris-for-health/) a partir da versão 2018.1.

## Tipos Primitivos Suportados 

|   Primitive Type           |   OAS JSON Type           |   OAS Format Type  |
|----------------------------|---------------------------|--------------------|
|   %Binary                  |   string                  |                    |
|   %xsd.base64Binary        |   string                  |                    |
|   %Boolean                 |   boolean                 |                    |
|   %Date                    |   string                  |   date             |
|   %PosixTime               |   string                  |   date-time        |
|   %StringTimeStamp         |   string                  |   date-time        |
|   %TimeStamp               |   string                  |   date-time        |
|   %DateTime                |   string                  |   date-time        |
|   %Currency                |   number                  |                    |
|   %Float                   |   number                  |                    |
|   %Decimal                 |   number                  |                    |
|   %Numeric                 |   number                  |                    |
|   %Double                  |   number                  |                    |
|   %Float                   |   number                  |                    |
|   %BigInt                  |   integer                 |   int64            |
|   %xsd.int                 |   integer                 |                    |
|   %xsd.long                |   integer                 |   int64            |
|   %Integer                 |   integer                 |                    |
|   %SmallInt                |   integer                 |   int32            |
|   %Time                    |   string                  |                    |
|   %TinyInt                 |   integer                 |                    |
|   %xsd.byte                |   integer                 |   int32            |
|   %String                  |   string                  |                    |
|   %Stream.FileBinary       |   string                  |   binary           |
|   %Stream.Filecharacter    |   string                  |                    |
|   %Stream.GlobalBinary     |   string                  |   binary           |
|   %Stream.GlobalCharacter  |   string                  |                    |
|   %Status                  |   special type as output  |                    |

## Tipos Complexos (Propriedades) Suportados

|   Property Type             |   OAS JSON Type                   |   OAS Format Type  |
|-----------------------------|-----------------------------------|--------------------|
|   %BigInt                   |   integer                         |   int64            |
|   %Binary                   |   string                          |                    |
|   %Boolean                  |   boolean                         |                    |
|   %Char                     |   string                          |                    |
|   %Counter                  |   integer                         |   int64            |
|   %Currency                 |   number                          |                    |
|   %Date                     |   string                          |   date             |
|   %DateTime                 |   string                          |   date-time        |
|   %Decimal                  |   number                          |                    |
|   %Double                   |   number                          |                    |
|   %EnumString               |   string                          |                    |
|   %ExactString              |   string                          |                    |
|   %Integer                  |   integer                         |   int64            |
|   %List                     |   array                           |                    |
|   %ListOfBinary             |   array                           |                    |
|   %Name                     |   string                          |                    |
|   %Numeric                  |   number                          |                    |
|   %SmallInt                 |   integer                         |                    |
|   %Status                   |   string                          |   binary           |
|   %String                   |   string                          |                    |
|   %Time                     |   string                          |                    |
|   %TimeStamp                |   string                          |   date-time        |
|   %TinyInt                  |   integer                         |                    |
|   %Stream.GlobalCharacter   |   string                          |                    |
|   %Stream.GlobalBinary      |   string                          |   binary           |
|   %Stream.GlobalCharacter   |   string                          |                    |
|   %Stream.GlobalBinary      |   string                          |   binary           |
|   Complex Type              |   object                          |                    |
|   list Of (Primitive Type)  |   array of (Primitive JSON Type)  |                    |
|   list Of (Complex Type)    |   array of (Complex Type)         |                    |
|   array Of (Primitive)	      |   array of (Primitive JSON Type)  |                    |
|   array Of (Complex Type)   |   array of (Complex Type)         |                    |
|   RelationShip              |   object or array                 |                    |
