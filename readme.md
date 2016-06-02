## MoIP Subscriptions

**MoIP Assinaturas** (Pagamentos Recorrentes) é um produto da **[MoIP](http://moip.com.br)**.

A Presente biblioteca cliente visa facilitar a integração / implementação da API de Assinaturas em projetos PHP.

### Sobre o Projeto
O Projeto utiliza o framework ActiveResource **[Restinga](https://github.com/artesaos/restinga)**.

### Status
Status atual da Cobertura do Pacote

| Endpoint                            | Progresso Implementação    | Progresso Documentação |
| ----------------------------------- | -------------------------- | ---------------------- |
| `coupons` (Cupons)                  | 100%                       | 100%                   |
| `plans` (Planos)                    | 100%                       | 0%                     |
| `customers` (Clientes)              | 100%                       | 0%                     |
| `subscriptions` (Assinaturas)       | 100%                       | 0%                     |
| `- invoices` (Faturas)              | 75%                        | 0%                     |
| `--- payments` (Pagamentos)         | 100%                       | 0%                     |
| `--- retries` (Re-rentativas)       | 0%                         | 0%                     |
| `users/preferences` (Preferências)  | 0%                         | 0%                     |





### Instalação
Para instalar a biblioteca **moip-subscriptions**, você deve utilizar o composer para incluí-la como dependência em seu projeto.

```
composer require artesaos/moip-subscriptions
```


### Configuração

Após instalar a biblioteca, você precisará configurar seu token e chave da API, bem como indicar se as chamadas serão feitas no ambiente de produção ou não:


```php
use Artesaos\MoIPSubscriptions\MoIPSubscriptions;

$token = '0011001100110011001100110011';
$key = '10101010101010101010010101010101010';
$production = false;

// A chamada setCredentials recebe 3 parametros
// O Token da API, a Chave da API e a indicação de produção ou não (true/false)
MoIPSubscriptions::setCredentials($token, $key, $production);
```

### Regra Geral:

Cada recurso / classe da API funciona praticamente da mesma forma, porem todos os métodos disponíveis estão (ou serão) documentados nesse manual.
Alguns métodos herdados da biblioteca Restinga como `$resource->destroy()` mesmo que disponíveis, não fazem efeito contra a API.


### Gerenciamento de Erros

Para gerenciar os erros que a API poderá exibir, você pode utilizar o método `->hasErrors()` para descobrir se houve algum erro na requisição, e o método `->getErrors()` que por sua vêz tem métodos como `->first()` e `->all()`.

O exemplo a seguir de gerenciamento de erros é baseado na classe `Plan`, mas não se preocupe que essa classe ainda não está coberta, você só precisa entender o fluxo dos erros agora.

Vamos no exemplo tentar alterar um plano com valor negativo, o que já sabemos que a API não permite. Faremos isso apenas para demonstrar o tratamento de errors.

```php

use Artesaos\MoIPSubscriptions\Resources\Plan;

// Busca um Plano Já cadastrado com código 'plan123'
$plan = Plan::find('plan123');

// Configuramos um valor negativo no plano
$plan->amount = -2200;

// Se o plano não pode ser salvo
if(!$plan->save()) {
    // e se Existe alguma mensagem de erro disponível
    if ($plan->hasErrors()) {
        // Método 1: obter um array com as mensagens de erro
        $errors = $plan->getErrors()->all();
        // Método 2: obter apenas o primeiro erro (caso hajam mais de 1 erro)
        $error = $plan->getErrors()->first();
    }

}
```

## Cupons de Desconto

Cupons de desconto são uma forma de oferecer promoções ou condições especiais a um cliente.



#### Criando um Cupon

Para Conhecer todos os campos, opções e regras disponíveis, visite: [http://dev.moip.com.br/assinaturas-api/?json#criar-um-coupon-post](http://dev.moip.com.br/assinaturas-api/?json#criar-um-coupon-post)

```php
use Artesaos\MoIPSubscriptions\Resources\Coupon;

// Nova instancia da classe Coupon
$coupon = new Coupon();

// O código do cupon
$coupon->code = 'LARAVEL50';
$coupon->name = 'Laravel Brasil - 50% de Desconto';
$coupon->description = 'Desconto de 50% para os artesãos';
$coupon->discount = [
    // Desconto de 50% == 5000
    'value' => '5000',
    // Marca o tipo do desconto
    'type' => 'percent',
];

$coupon->status = 'active';

$coupon->save();
```

#### Obtendo todos os cupons

Para Conhecer todos os campos, opções e regras disponíveis, visite: [http://dev.moip.com.br/assinaturas-api/?json#listar-coupons-get](http://dev.moip.com.br/assinaturas-api/?json#listar-coupons-get)

```php
use Artesaos\MoIPSubscriptions\Resources\Coupon;

$coupons = Coupon::all();
```

#### Consultar um Cupon

Para Conhecer todos os campos, opções e regras disponíveis, visite: 
[http://dev.moip.com.br/assinaturas-api/?json#consultar-coupon-get](http://dev.moip.com.br/assinaturas-api/?json#consultar-coupon-get)

```php
use Artesaos\MoIPSubscriptions\Resources\Coupon;

$coupon = Coupon::find('LARAVEL50');
```


#### Atualizando um Cupon

**Método não Listado na Documentado Oficial da API**

```php
use Artesaos\MoIPSubscriptions\Resources\Coupon;

$coupon = Coupon::find('LARAVEL50');

$coupon->description = 'Laravel é fantástico, desconto de 50%';

$coupon->update();
```

#### Ativar ou Desativar um Cupon

Para Conhecer todos os campos, opções e regras disponíveis, visite:
[http://dev.moip.com.br/assinaturas-api/?json#ativar-e-inativar-coupons-put](http://dev.moip.com.br/assinaturas-api/?json#ativar-e-inativar-coupons-put)

```php
use Artesaos\MoIPSubscriptions\Resources\Coupon;

$coupon = Coupon::find('LARAVEL50');

// ativar
$coupon->activate();

// desativar
$coupon->inactivate();
```

#### Associação em assinaturas

Apesar dessa opção estar lista da documentação oficial na aba de cupons, o mesmo é feito através do endpoint **subscriptions** por isso será documentada posteriormente na sessão adequada.
