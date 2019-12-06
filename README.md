# CodeIgniter 4  Integration PagSeguro API

### Em desenvolvimento.

## Conteúdo:

- [Estrutura](#estrutura "Estrutura")
- [Utilização](#utiliza%C3%A7%C3%A3o "Utilização")
- [Funcionamento](#funcionamento "Funcionamento")
- [Banco de dados](#banco-de-dados-utilizado "Banco de dados")

## Features:

- Geração de boleto pela API
- Pagamento por cartão de crédito
- Callback ao atualizar algum status de pagamento
- Validação com um código de referência unico
- Envio de confirmação por e-mail do status do pedido
- Boleto em Lightbox em um modal
- Loader para aguardar requisição de pagamento
- Logs a cada status da transação

## A fazer:

- [ ] Verificar qual bandeira do cartão
- [ ] Tratamento de erros do PagSeguro
- [ ] Pagamento de cartão com juros
- [ ] Colocar do boleto na função de notificar por e-mail 
- [ ] Utilizar o cURL do ci4
- [ ] Finalização de campos do formulário
- [ ] Aviso de vencimento de boleto a 1 dia do vencimento
- [ ] Deixar uma view apenas para listagem

## Estrutura:
| Tipo | Nome | Razão |
| ------ | ------ | ------ |
| Controller | Home.php | Status para verificar as variáveis |
| Controller | Sessao.php | Responsável por gerar as sessões de pagamento |
| Controller | Listagem.php | Listagem das transações |
| Controller | Notificacao.php | Receber a requisição do PagSeguro |
| Controller | Email.php | Enviar status de atualizações por e-mail |
| Controller | Pagar.php | Enviar as requisições ao PagSeguro |
| Controller | Transacoes.php | Comunicação o banco de dados |
| Helper | pagamento_helper.php | Conversão de valores para o cliente |
| Model | TransacoesModel.php | Operações no banco de dados |


## Utilização:

1. Seguir a instalação do PHP. Caso dê algum erro de instalação do CI4 com o PHP, siga estes passos [Instalação PHP](https://github.com/matheuscastroweb/ci4-crud/blob/master/README.md "Instalação PHP").

```php
#-----------------------------
# Extensões necessárias do php.ini
#-----------------------------
extension=mbstring
extension=mysqli
extension=curl
```

2.  Criar uma conta no [PagSeguro Sandbox](https://sandbox.pagseguro.uol.com.br/ "PagSeguro Sandbox"). A documentação pode ser acessar através do link [Documentação PagSeguro](https://dev.pagseguro.uol.com.br/docs "Documentação PagSeguro"). Ao alterar o `api.mode ` para `production` acessará a URL de produção do PagSeguro.


```php
#-----------------------------
# API PagSeguro - Alterar no .env
#-----------------------------
api.mode	= development
api.email	= seu_email
api.token	= seu_token
```

3. Alterar o email de teste disponibizado no PagSeguro `./Views/home` no campo `email` para utilizar em modo desenvolvimento do PagSeguro e fazer os pagamentos. 

4. Para o envio de e-mails basta alterar as para as respectivas. Em padrão o campo `mail.using` vem como `false`, para realizar o envio de e-mail . O serviço de e-mail utilizado foi o [Mailtrap](https://mailtrap.io/ "Mailtrap").

```php
#--------------------------------------------------------------------
# Config Mail
#--------------------------------------------------------------------
mail.using   = false
mail.host    = host
mail.user    = user
mail.pass    = pass
mail.port    = port
```

5. Para utilizar o módulo de notificação em localhost, basta acessar o PagSeguro e simular uma troca de status.

> **OBS.:** Sempre ao atualizar algum parâmetro do .env reinicie o servidor php.
> **OBS.:** Caso a base url não seja localhost:8080, configurar neste documentos para gerar as sessões `public/assets/js/sessao.js `

6. Verificar se todos os parâmetros do PagSeguro estão configurados como `SIM`. 

![Home](https://user-images.githubusercontent.com/45601574/70171446-7bc6c980-16ad-11ea-9985-342c2cd936b2.png)


## Funcionamento:
Testes realizados em sandbox com geração de nome e CPF inválidos somentes para testes. 

### Listagem de todas transações:

![Listagem](https://user-images.githubusercontent.com/45601574/70171703-0b6c7800-16ae-11ea-8661-7ead0f8a0827.png)

### Pagamento cartão:

![Pagamento-cartao](https://user-images.githubusercontent.com/45601574/70101423-90568380-1613-11ea-9f03-adfea52c4329.gif)

### Pagamento boleto:

![Pagamento](https://user-images.githubusercontent.com/45601574/70101422-90568380-1613-11ea-9bb8-da7de6576753.gif)

## Banco de dados utilizado:
Utilizar a migration `AddTransacao` pela CLI conforme abaixo ou adicionar manualmente com o código SQL abaixo. Veja aqui como utilizar as [Migrations do CI4](https://codeigniter4.github.io/userguide/dbmgmt/migration.html#command-line-tools "Migrations do CI4").


```php
#--------------------------------------------------------------------
# Comandos úteis para utilização das migrations
#--------------------------------------------------------------------

#Criação das migrations
php spark migrate:create

#Rodar as migrations
php spark migrate

```

```sql
CREATE OR REPLACE DATABASE ci4_integration_pagseguro;
```

```sql
USE ci4_integration_pagseguro;

CREATE OR REPLACE TABLE transacao (
    id INT PRIMARY KEY AUTO_INCREMENT,
    id_pedido INT NOT NULL,
    id_cliente INT NOT NULL, 
    codigo_transacao VARCHAR(255) NOT NULL,
    tipo_transacao TINYINT(1) NOT NULL,
    referencia_transacao VARCHAR(255) NOT NULL,
    status_transacao VARCHAR(45)  NOT NULL,
    valor_transacao DOUBLE  NOT NULL,
    url_boleto VARCHAR(255),
    created_at DATETIME NOT NULL,
    updated_at DATETIME NOT NULL,
    deleted_at DATETIME DEFAULT NULL 
    );
```

