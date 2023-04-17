AWS

1o. serviço:
Criar api no "API Gateway" manualmente
- Create resources items

2o. serviço:
Criar tabela no dynamoDB
Tabela DIOItems
id como partition key

3o. serviço:
Criar lambda
dio_put_items_function

Ajustar configuração de acesso a partir da tab "configuration"
em "Execution Role"

Nessa role, por exemplo, dio_put_items_function-role-3ac79b7c adicionar nova política:
"Add Permissions" - "Create Inline Policy"

Choose service: DynamoDB
Manual actions: adicionar "putItem"

Resources: "Add ARN"
Specify ARN for table:
arn:aws:dynamodb:us-east-1:------------:table/DIOItems por exemplo

em Review Policy dê o nome de putItem_policy

Ir para o API Gateway para fazer a integração
em Items -> Actions -> "Create Method" -> POST (no combo box)

Marque "Use Lambda Proxy Integration" para poder enxergar o event.body dentro do código do lambda

Lambda function: dio_put_items_function

Em "Method Request" fazer a integração com o "authorizer":

Antes fazer o deploy em "Actions" -> "Deployment stage" -> [New Stage]
Stage name: development

Aí você obtém: Invoke URL: https://-----------.execute-api.us-east-1.amazonaws.com/development

Fazer um request no POSTMAN:
Body payload request:
{
    "id": "003",
    "price": 600
}

4o. serviço:
Cognito
Criar user pool:
Nome: DIOLiveUserPool (Step 5)
New Role Name: DIOLiveUserPool-SMS-Role
Nota: a parte de atributos fica no step 3

No step 1:
marque a opção "email" (Cognito user pool sign-in options)
Não precisou utilizar ou criar user attributes

Step 2: manter defaults
Step 3: manter defaults
Step 4: usar opção "send email with cognito"
Step 5: Colocar o nome do user pool
Nome: DIOLiveUserPool 
App client name: DIOLiveAppClient
Marque "Generate a client secret"

Em review clique no botão "Create pool"

Depois altere algumas configurações como:
criar Amazon Cognito Domain: diolivenew-valdir

Para criar o "Callback URL(s)" entre no app client DIOLiveAppClient e configure
"OAuth 2.0" 
Grant Types: "Authorization code grant" e "Implicit grant"
OpenID Connected scopes: "email" e "openid"
Em "Identity providers" selecione "Cognito user pool"


Para testar, vá ao postman e crie um get request:
Na aba "Authorization" use "Oauth 2.0"
Grant Type: Implicit
Callbak URL: https://example.com/
Auth URL: https://----------.auth.us-east-1.amazoncognito.com/login
Client ID: -----------------
Scope: email openid

copie o id token gerado no postman e vá ao site jwt.io e analise

Depois do teste vá ao serviço de "API Gateway" e configure o authorizer:
"Create New Authorizer"
e escolha "Cognito"
Nome: DIOCognitoAuthorizer

Depois, vá em "Resources" ---> POST ---> "Method Request"
Em "Authorization" aponte para o authorizer criado, se não aparecer, recarregue a página que vai aparecer

Depois, "Deploy API"

Testar no postman a inserção com o POST








