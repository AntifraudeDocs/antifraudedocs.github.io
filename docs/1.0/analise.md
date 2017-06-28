---
layout: page-classic-sidebar-left
title: Análise
featimg: MapAntifraud.png
previous: /docs/1.0/autenticacao
next: /docs/1.0/postnotification
---
---

Esta página descreve os campos do contrato do Antifraude Gateway, além de conter exemplos de requisições HTTP.  

## Hosts

**Test** https://riskhomolog.braspag.com.br  
**Live** https://risk.braspag.com.br

<a name="contract"></a>
  
## Atributos
-----------------------------------

**MerchantOrderId**{:.custom-attrib}  `required`{:.custom-tag} `100`{:.custom-tag} `string`{:.custom-tag}  
Número do Pedido da Loja.  

**TotalOrderAmount**{:.custom-attrib} `required`{:.custom-tag} `long`{:.custom-tag}  
Valor total do pedido em centavos.  
Ex.: 150000 (Valor equivalente a R$1.500,00)

**TransactionAmount**{:.custom-attrib} `required`{:.custom-tag} `long`{:.custom-tag}  
Valor da transação financeira a ser analisada.  
Ex.: 123456 Valor equivalente a R$1.234,56)

**Currency**{:.custom-attrib} `required`{:.custom-tag} `3`{:.custom-tag} `string`{:.custom-tag}  
Moeda.  
Mais informações em [ISO 4217 - Currency Codes](https://www.iso.org/iso-4217-currency-codes.html)
Ex.: BRL (Real Brasileiro) | USD(Dólar Americano)

**Provider**{:.custom-attrib} `required`{:.custom-tag} `15`{:.custom-tag} `string`{:.custom-tag}  
Nome do Provedor da Solução de Analise de Fraude.  
Enum.: ReDShield

**OrderDate**{:.custom-attrib} `default = DateTime.Now`{:.custom-tag} `optional`{:.custom-tag} `datetime`{:.custom-tag}  
Data do pedido.  
Ex.: 2016-12-09T19:16:38.155Z  
Obs.: Caso não envie a data do pedido, será gerada uma data pela Braspag.  

**BraspagTransactionId**{:.custom-attrib} `optional`{:.custom-tag} `Guid`{:.custom-tag}  
Id da transação no Pagador da Braspag.  
Ex.: ED3B6646-5B6E-451C-B3CF-4FF5E807CB69  
Obs.: Se o fluxo implementado ao seu negócio for a primeira chamada para fazer a análise de fraude atravpes do Antifraude Gateway e a segunda for a autorização através do Pagador, realizar uma terceira chamada para que as transações do Antifraude Gateway e Pagador sejam vinculadas. Para mais detalhes acessar: [Associar transação Pagador e Antifraude]({{ site.baseurl }}{% link docs/1.0/linktransaction.md %})  

**SplitingPaymentMethod**{:.custom-attrib} `default = None`{:.custom-tag} `optional`{:.custom-tag} `23`{:.custom-tag} `string`{:.custom-tag}  
Identifica se a autorização da transação é com um ou dois cartões ou com mais de um meio de pagamento, por exemplo, cartão de crédito e boleto bancário.  
Enum:  
None -> Pagamento com um cartão apenas.  
CardSplit -> Pagamento com mais de um cartão.  
MixedPaymentMethodSplit -> Pagamento com mais de um meio de pagamento.  

**IsRetryTransaction**{:.custom-attrib} `default = false`{:.custom-tag} `optional`{:.custom-tag} `bool`{:.custom-tag}  
Identifica que é uma retentativa de uma análise. Este campo deve ser enviado com valor igual a TRUE quando o código de retorno na primeira tentativa for igual a BP900, que identifica timeout entre Braspag e o Provedor. Este campo deve ser enviado somente quando Provedor igual a ReDShield.  

**Card.Number**{:.custom-attrib} `required`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Número do cartão de crédito.

**Card.Holder**{:.custom-attrib} `required`{:.custom-tag} `50`{:.custom-tag} `string`{:.custom-tag}  
Nome do cartão de crédito.  

**Card.ExpirationDate**{:.custom-attrib} `required`{:.custom-tag} `7`{:.custom-tag} `string`{:.custom-tag}  
Data de expiração do cartão de crédito.  
Ex.: 01/2023

**Card.CVV**{:.custom-attrib} `required`{:.custom-tag} `4`{:.custom-tag} `string`{:.custom-tag}  
Código de segurança do cartão de crédito.  

**Card.Brand**{:.custom-attrib} `required`{:.custom-tag} `10`{:.custom-tag} `string`{:.custom-tag}  
Bandeira do cartão de crédito.  
Bandeiras suportadas: Amex | Diners | Discover | JCB | Master | Dankort | Cartebleue | Maestro | Visa | Elo | Hipercard  

**Card.EciThreeDSecure**{:.custom-attrib}  `optional`{:.custom-tag} `1`{:.custom-tag} `string`{:.custom-tag}  
Código do ECI (Eletronic Commerce Indicator) de autenticação  

**Card.Token**{:.custom-attrib}  `optional`{:.custom-tag} `Guid`{:.custom-tag}  
Identificador do cartão de crédito salvo no Cartão Protegido.  
Obs.: Este campo pode ser enviado no lugar dos campos **Card.Number, Card.Holder, Card.ExpirationDate**, tornando-os assim como não obrigatórios. O sistema irá consumir o Cartão Protegido enviando este campo para preencher os citados.  

**Card.Alias**{:.custom-attrib}  `optional`{:.custom-tag} `64`{:.custom-tag} `string`{:.custom-tag}  
Alias (apelido) do cartão de crédito salvo no Cartão Protegido.  
Obs.: Este campo pode ser enviado na análise de fraude quando se deseja salvar um cartão no Cartão Protegido, em conjunto com o campo **Card.Save** igual a TRUE.  
Obs2.: Este campo pode ser enviado no lugar dos campos **Card.Number, Card.Holder, Card.ExpirationDate**, tornando-os assim como não obrigatórios. O sistema irá consumir o Cartão Protegido enviando este campo para preencher os citados.  
Obs3.: Este campo perde em prioridade caso o campo **Card.Token** seja enviado também.  

**Card.Save**{:.custom-attrib} `default = false`{:.custom-tag} `optional`{:.custom-tag} `bool`{:.custom-tag}  
Indica se os dados do cartão de crédito serão armazenados no Cartão Protegido. O Token gerado na plataforma Cartão Protegido associado aos dados de cartão de crédito, retornará no response da análise de fraude através campo **Card.Token**.  
Obs.: Os seguintes campos serão salvos no Cartão Protegido: **Card.Number, Card.Holder, Card.ExpirationDate**.  
Obs2.: A ação de salvar os dados do cartão de crédito, só será feita se a loja possuir o produto Cartão Protegido contratado.  

**Billing.Street**{:.custom-attrib}  `optional`{:.custom-tag} `24`{:.custom-tag} `string`{:.custom-tag}  
Logradouro do endereço de cobrança.  

**Billing.Number**{:.custom-attrib}  `optional`{:.custom-tag} `5`{:.custom-tag} `string`{:.custom-tag}  
Número do endereço de cobrança.

**Billing.Complement**{:.custom-attrib}  `optional`{:.custom-tag} `14`{:.custom-tag} `string`{:.custom-tag}  
Complemento do endereço de cobrança.

**Billing.Neighborhood**{:.custom-attrib}  `optional`{:.custom-tag} `15`{:.custom-tag} `string`{:.custom-tag}  
Bairro do endereço de cobrança.

**Billing.City**{:.custom-attrib}  `optional`{:.custom-tag} `20`{:.custom-tag} `string`{:.custom-tag}  
Cidade do endereço de cobrança.

**Billing.State**{:.custom-attrib}  `optional`{:.custom-tag} `2`{:.custom-tag} `string`{:.custom-tag}  
Estado do endereço de cobrança.

**Billing.Country**{:.custom-attrib}  `optional`{:.custom-tag} `2`{:.custom-tag} `string`{:.custom-tag}  
País do endereço de cobrança.  
Mais informações em [ISO 2-Digit Alpha Country Code](https://www.iso.org/obp/ui)

**Billing.ZipCode**{:.custom-attrib}  `optional`{:.custom-tag} `9`{:.custom-tag} `string`{:.custom-tag}  
Código postal do endereço de cobrança.

**Shipping.Street**{:.custom-attrib}  `optional`{:.custom-tag} `24`{:.custom-tag} `string`{:.custom-tag}  
Logradouro do endereço de entrega.

**Shipping.Number**{:.custom-attrib}  `optional`{:.custom-tag} `5`{:.custom-tag} `string`{:.custom-tag}  
Número do endereço de entrega.

**Shipping.Complement**{:.custom-attrib}  `optional`{:.custom-tag} `14`{:.custom-tag} `string`{:.custom-tag}  
Complemento do endereço de entrega.

**Shipping.Neighborhood**{:.custom-attrib}  `optional`{:.custom-tag} `15`{:.custom-tag} `string`{:.custom-tag}  
Bairro do endereço de entrega.

**Shipping.City**{:.custom-attrib}  `optional`{:.custom-tag} `20`{:.custom-tag} `string`{:.custom-tag}  
Cidade do endereço de entrega.

**Shipping.State**{:.custom-attrib}  `optional`{:.custom-tag} `2`{:.custom-tag} `string`{:.custom-tag}  
Estado do endereço de entrega.

**Shipping.Country**{:.custom-attrib}  `optional`{:.custom-tag} `2`{:.custom-tag} `string`{:.custom-tag}  
País do endereço de entrega.  
Mais informações em [ISO 2-Digit Alpha Country Code](https://www.iso.org/obp/ui)

**Shipping.ZipCode**{:.custom-attrib}  `optional`{:.custom-tag} `9`{:.custom-tag} `string`{:.custom-tag}  
Código postal do endereço de entrega.

**Shipping.Email**{:.custom-attrib}  `optional`{:.custom-tag} `60`{:.custom-tag} `string`{:.custom-tag}  
E-mail do responsável a receber o produto no endereço de entrega.

**Shipping.FirstName**{:.custom-attrib}  `optional`{:.custom-tag} `30`{:.custom-tag} `string`{:.custom-tag}  
Primeiro nome do responsável a receber o produto no endereço de entrega.

**Shipping.MiddleName**{:.custom-attrib}  `optional`{:.custom-tag} `1`{:.custom-tag} `string`{:.custom-tag}  
Nome do meio do responsável a receber o produto no endereço de entrega.

**Shipping.LastName**{:.custom-attrib}  `optional`{:.custom-tag} `30`{:.custom-tag} `string`{:.custom-tag}  
Último nome do responsável a receber o produto no endereço de entrega.

**Shipping.Phone**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Número do telefone do responsável a receber o produto no endereço de entrega.  
Ex.: 552121114700  
55 = Código do País  
21 = DDD do estado  
21114700 = Número do Telefone  

**Shipping.WorkPhone**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Número do telefone de trabalho do responsável a receber o produto no endereço de entrega.  
Ex.: 552121114701  
55 = Código do País  
21 = DDD do estado  
21114701 = Número do Telefone  

**Shipping.Mobile**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Número do celular do responsável a receber o produto no endereço de entrega.  
Ex.: 5521988776655  
55 = Código do País  
21 = DDD do estado  
988776655 = Número do Celular  

**Shipping.ShippingMethod**{:.custom-attrib}  `optional`{:.custom-tag} `27`{:.custom-tag} `string`{:.custom-tag}  
Meio de entrega.  
Enum:  
SameDay = Meio de entrega no mesmo dia  
NextDay = Meio de entrega no próximo dia  
TwoDay = Meio de entrega em dois dias  
ThreeDay = Meio de entrega em três dias  
LowCost = Meio de entrega de baixo custo  
Pickup = Retirada na loja  
CarrierDesignatedByCustomer = Meio de entrega designada pelo comprador  
International = Meio de entrega internacional  
Military = Meio de entrega militar  
Other = Outro meio de entrega  
None = Sem meio de entrega, pois é um serviço ou assinatura.  

**Shipping.Comment**{:.custom-attrib}  `optional`{:.custom-tag} `160`{:.custom-tag} `string`{:.custom-tag}  
Referências do endereço de entrega.

**Customer.MerchantCustomerId**{:.custom-attrib}  `required`{:.custom-tag} `16`{:.custom-tag} `string`{:.custom-tag}  
Número do documento de identificação do comprador.  
Ex.: CPF | CNPJ | Passaporte

**Customer.FirstName**{:.custom-attrib}  `required`{:.custom-tag} `30`{:.custom-tag} `string`{:.custom-tag}  
Primeiro nome do comprador.

**Customer.MiddleName**{:.custom-attrib}  `optional`{:.custom-tag} `1`{:.custom-tag} `string`{:.custom-tag}  
Nome do meio do comprador.

**Customer.LastName**{:.custom-attrib}  `required`{:.custom-tag} `30`{:.custom-tag} `string`{:.custom-tag}  
Último nome do comprador do comprador.

**Customer.BirthDate**{:.custom-attrib}  `required`{:.custom-tag} `date`{:.custom-tag}  
Data de nascimento do comprador.  
Ex.: 1983-10-01

**Customer.Gender**{:.custom-attrib}  `optional`{:.custom-tag} `6`{:.custom-tag} `string`{:.custom-tag}  
Sexo do comprador.  
Enum: Male | Female

**Customer.Email**{:.custom-attrib}  `optional`{:.custom-tag} `60`{:.custom-tag} `string`{:.custom-tag}  
E-mail do comprador.

**Customer.Phone**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Telefone do comprador.
Ex.: 552121114700  
55 = Código do País  
21 = DDD do estado  
21114700 = Número do Telefone  

**Customer.Ip**{:.custom-attrib}  `optional`{:.custom-tag} `15`{:.custom-tag} `string`{:.custom-tag}  
Endereço de IP do comprador.

**Customer.Mobile**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Celular do comprador.  
Ex.: 5521988776655  
55 = Código do País  
21 = DDD do estado  
988776655 = Número do Celular  

**Customer.WorkPhone**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Telefone de trabalho do comprador.  
Ex.: 552121114701  
55 = Código do País  
21 = DDD do estado  
21114701 = Número do Telefone  

**Customer.BrowserFingerPrint**{:.custom-attrib}  `required`{:.custom-tag} `6005`{:.custom-tag} `string`{:.custom-tag}  
Impressão digital de dispositivos e geolocalização real do IP do comprador.  
[Configuração do Fingerprint]({{ site.baseurl }}{% link docs/1.0/fingerprint.md %})

**Customer.Status**{:.custom-attrib}  `optional`{:.custom-tag} `8`{:.custom-tag} `string`{:.custom-tag}  
Status do comprador na loja.  
Enum:  
New = Identifica quando o comprador é novo na loja, nunca fez uma compra.  
Existing = Identifica quando o comprador é existente na loja, já realizou uma compra.  

**CartItem[n].ProductName**{:.custom-attrib}  `optional`{:.custom-tag} `50`{:.custom-tag} `string`{:.custom-tag}  
Nome do produto.

**CartItem[n].UnitPrice**{:.custom-attrib}  `optional`{:.custom-tag} `long`{:.custom-tag}  
Preço unitário do produto.
Ex.: 10950 (Valor equivalente a R$109,50)

**CartItem[n].MerchantItemId**{:.custom-attrib}  `optional`{:.custom-tag} `30`{:.custom-tag} `string`{:.custom-tag}  
ID do produto na loja.

**CartItem[n].Sku**{:.custom-attrib}  `optional`{:.custom-tag} `12`{:.custom-tag} `string`{:.custom-tag}  
Sku do produto.

**CartItem[n].Quantity**{:.custom-attrib}  `optional`{:.custom-tag} `int`{:.custom-tag}  
Quantidade do produto.

**CartItem[n].OriginalPrice**{:.custom-attrib}  `optional`{:.custom-tag} `long`{:.custom-tag}  
Preço original do produto.
Ex.: 11490 (Valor equivalente a R$114,90)

**CartItem[n].GiftMessage**{:.custom-attrib}  `optional`{:.custom-tag} `160`{:.custom-tag} `string`{:.custom-tag}  
Mensagem de presente.

**CartItem[n].Description**{:.custom-attrib}  `optional`{:.custom-tag} `76`{:.custom-tag} `string`{:.custom-tag}  
Nome do produto.

**CartItem[n].ShippingInstructions**{:.custom-attrib}  `optional`{:.custom-tag} `160`{:.custom-tag} `string`{:.custom-tag}  
Instruções de entrega do produto.

**CartItem[n].ShippingMethod**{:.custom-attrib}  `optional`{:.custom-tag} `27`{:.custom-tag} `string`{:.custom-tag}  
Meio de entrega do produto.  
Enum:  
SameDay = Meio de entrega no mesmo dia  
NextDay = Meio de entrega no próximo dia  
TwoDay = Meio de entrega em dois dias  
ThreeDay = Meio de entrega em três dias  
LowCost = Meio de entrega de baixo custo  
Pickup = Retirada na loja  
CarrierDesignatedByCustomer = Meio de entrega designada pelo comprador  
International = Meio de entrega internacional  
Military = Meio de entrega militar  
Other = Outro meio de entrega  
None = Sem meio de entrega, pois é um serviço ou assinatura.  

**CartItem[n].ShippingTranckingNumber**{:.custom-attrib}  `optional`{:.custom-tag} `19`{:.custom-tag} `string`{:.custom-tag}  
Número de rastreamento do produto.

**CartItem[n].Passenger.FirstName**{:.custom-attrib} `optional`{:.custom-tag} `255`{:.custom-tag} `string`{:.custom-tag}  
Primeiro nome do passageiro.

**CartItem[n].Passenger.MiddleName**{:.custom-attrib} `optional`{:.custom-tag} `255`{:.custom-tag} `string`{:.custom-tag}  
Nome do meio do passageiro.

**CartItem[n].Passenger.LastName**{:.custom-attrib} `optional`{:.custom-tag} `255`{:.custom-tag} `string`{:.custom-tag}  
Último nome do comprador do passageiro.

**CartItem[n].Passenger.DateOfBirth**{:.custom-attrib} `optional`{:.custom-tag} `date`{:.custom-tag}  
Data de nascimento do passageiro.  
Ex.: 1985-07-22

**CartItem[n].Passenger.PassangerId**{:.custom-attrib} `optional`{:.custom-tag} `50`{:.custom-tag} `string`{:.custom-tag}  
ID do passageiro a quem o passageiro foi emitido.  

**CartItem[n].Passenger.Status**{:.custom-attrib} `optional`{:.custom-tag} `15`{:.custom-tag} `string`{:.custom-tag}  
Classificação da empresa aérea.  
Enum.: Gold | Platinum  

**CartItem[n].Passenger.PassengerType**{:.custom-attrib} `optional`{:.custom-tag} `35`{:.custom-tag} `string`{:.custom-tag}  
Tipo do passageiro.  
Enum:  
Adult = Adulto  
Child = Criança  
Infant = Infantil  
Youth = Adolescente  
Student = Estudante  
SeniorCitizen = Idoso  
Military = Militar  

**CartItem[n].Passenger.Email**{:.custom-attrib} `optional`{:.custom-tag} `100`{:.custom-tag} `string`{:.custom-tag}  
E-mail do passageiro.

**CartItem[n].Passenger.Phone**{:.custom-attrib} `optional`{:.custom-tag} `35`{:.custom-tag} `string`{:.custom-tag}  
Telefone do passageiro.
Ex.: 552121114700  
55 = Código do País  
21 = DDD do estado  
21114700 = Número do Telefone  

<!--**CustomerConfigurationData.ServiceId**{:.custom-attrib}  `optional`{:.custom-tag} `50`{:.custom-tag} `string`{:.custom-tag}  
Id do serviço no sistema de risco. Esse campo geralmente é definido por alguma configuração, mas em algumas situações, você pode querer usar a opção baseada em solicitação dinâmica.
Para o ReDShield isso define qual serviço de triagem de fraude deve ser usado.-->

<!--**CustomerConfiguration.RiskBrand**{:.custom-attrib}  `optional`{:.custom-tag} `50`{:.custom-tag} `long`{:.custom-tag}  
Bandeira de risco do pedido.  -->

**CustomerConfiguration.Website**{:.custom-attrib}  `optional`{:.custom-tag} `60`{:.custom-tag} `string`{:.custom-tag}  
Website da loja.

<!--**CustomerConfigurationData.AccessTokenS**{:.custom-attrib}  `optional`{:.custom-tag} `255`{:.custom-tag} `string`{:.custom-tag}  -->

**MerchantDefinedData.Key**{:.custom-attrib}  `optional`{:.custom-tag} `string`{:.custom-tag}  
Campo definido junto ao provedor de antifraude.

**MerchantDefinedData.Value**{:.custom-attrib} `optional`{:.custom-tag} `string`{:.custom-tag}  
Campo definido junto ao provedor de antifraude.  

Obs.: Entre 15 campos disponíveis para customização, 5 são de tamanho de 256 caracteres alphanuméricos e 10 são de tamanho de 32 caracteres alphanuméricos.  
Obs2.: Já existem 2 campos além dos 15 disponíveis, que a loja poderá enviar, que são:  
     - Segment = Código do segmento do lojista (MCC). Caso o mesmo se encaixe em mais de um segmento, enviar o código do segmento principal que a loja atua.  
     - MerchantId = Identificador da loja no lojista. Este identificar não é o identificador da loja no antifraude na Braspag. Exemplo de envio, quando a loja trabalha com marketplace, onde poderá enviar o identificador da loja vendedora (Seller).  

**Travel.CompleteRoute**{:.custom-attrib}  `optional`{:.custom-tag} `100`{:.custom-tag} `string`{:.custom-tag}  
Rota completa da viagem. Concatenação das pernas (código do aeroporto) de origem e destino da viagem no formato, ORIG1-DEST1:ORIG2-DEST2.  
Ex.: SFO-JFK:JFK-LHR:LHR-CDG

**Travel.DepartureTime**{:.custom-attrib}  `optional`{:.custom-tag} `datetime`{:.custom-tag}  
Data de partida do vôo.  
Ex.: 2017-03-01T15:10:15.258Z

**Travel.JourneyType**{:.custom-attrib}  `optional`{:.custom-tag} `100`{:.custom-tag} `string`{:.custom-tag}  
Tipo de viagem.  
Enum.: OneWayTrip | RoundTrip.  

**Travel.TravelLeg[n].Origin**{:.custom-attrib}  `optional`{:.custom-tag} `5`{:.custom-tag} `string`{:.custom-tag}  
Código do aeroporto de origem da viagem.  
Ex.: SFO

**Travel.TravelLeg[n].Destination**{:.custom-attrib}  `optional`{:.custom-tag} `5`{:.custom-tag} `string`{:.custom-tag}  
Código do aeroporto de destino da viagem.  
Ex.: JFK  

<a style="float: right;" href="#attributes"><i class="fa fa-angle-double-up fa-fw"></i></a>

<a name="http_operations"></a>

## Operações HTTP
-----------------------------------

`POST`{:.http-post} [https://riskhomolog.braspag.com.br/Analysis/](#post_analise){:.custom-attrib}  
Análise da transação  

`GET`{:.http-get} [https://riskhomolog.braspag.com.br/Analysis/{Id}](#get_analise){:.custom-attrib}  
Obter detalhes da análise da transação  

<a style="float: right;" href="#http_operations"><i class="fa fa-angle-double-up fa-fw"></i></a>

<a name="post_analise"></a>

#### `POST`{:.http-post} Análise da Transação 
-------------------------------------------

**REQUEST:**  

``` http
POST https://riskhomolog.braspag.com.br/Analysis/ HTTP/1.1
Host: {antifraude endpoint}
Authorization: Bearer {access_token}
Content-Type: application/json
MerchantId: {Id da Loja no Antifraude Gateway}
```

``` json
{
  "MerchantOrderId": "4493d42c-8732-4b13-aadc-b07e89732c26",
  "TotalOrderAmount": 1500,
  "TransactionAmount": 1000,
  "Currency": "BRL",
  "Provider": "RedShield",
  "OrderDate": "2016-12-09 12:35:58.852",
  "BraspagTransactionId":"a3e08eb2-2144-4e41-85d4-61f1befc7a3b",
  "SplitingPaymentMethod": "None",
  "IsRetryTransaction": false,
  "Card": {
  "Number" : "4000111231110112",
    "Holder": "Fernando Test",
    "ExpirationDate": "12/2023",
    "Cvv": "999",
    "Brand": "VISA",
    "EciThreeDSecure": "5"
  },
  "Billing": {
    "Street": "Av Marechal Camara",
    "Number": "12345",
    "Complement": "Sala 123",
    "Neighborhood": "Centro",
    "City": "Rio de Janeiro",
    "State": "RJ",
    "Country": "BR",
    "ZipCode": "20080123"
  },
  "Shipping": {
        "Street": "Avenida Rio Branco",
        "Number": "30000",
        "Complement": "sl 123",
        "Neighborhood": "Centro",
        "City": "Rio de Janeiro",
        "State": "RJ",
        "Country": "BR",
        "ZipCode": "123456789",
        "Email": "emailentrega@dominio.com.br",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silvao",
        "ShippingMethod": "SameDay",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Comment": "Em frente ao 322"
    },
  "Customer": {
        "MerchantCustomerId": "10050665740",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "BirthDate": "2016-12-09",
        "Gender": "Male",
        "Email": "emailcomprador@dominio.com.br",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Ip": "127.0.0.1",
        "BrowserFingerprint": "04003hQUMXGB0poNf94lis1ztuLYRFk+zJ17aP79a9O8mWOBmEnKs6ziAo94ggAtBvKEN6/FI8Vv2QMAyHLnc295s0Nn8akZzRJtHwsEilYx1P+NzuNQnyK6+7x2OpjJZkl4NlfPt7h9d96X/miNlYT65UIY2PeH7sUAh9vKxMn1nlPu2MJCSi12NBBoiZbfxP1Whlz5wlRFwWJi0FRulruXQQGCQaJkXU7GWWZGI8Ypycnf7F299GIR12G/cdkIMFbm6Yf0/pTJUUz1vNp0X2Zw8QydKgnOIDKXq4HnEqNOos1c6njJgQh/4vXJiqy0MXMQOThNipDmXv9I185O+yC2f3lLEO0Tay66NZEyiLNePemJKSIdwO9O5ZtntuUkG6NTqARuHStXXfwp8cyGF4MPWLuvNvEfRkJupBy3Z8hSEMEK7ZWd2T2HOihQxRh4qp+NANqYKBTl3v6fQJAEKikeSQVeBN8sQqAL0BZFaIMzbrnMivi6m6JRQUIdvEt+MbJEPFc0LjRycC5ApUmJO+Aoo9VKL1B8ftMSQ1iq1uTKn16ZOmDpzZrZhMPbH83aV0rfB2GDXcjpghm9klVFOw7EoYzV7IDBIIRtgqG9KZ+8NH/z6D+YNUMLEUuK1N2ddqKbS5cKs2hplVRjwSv7x8lMXWE7VDaOZWB8+sD1cMLQtEUC0znzxZ4bpRaiSy4dJLxuJpQYAFUrDlfSKRv/eHV3QiboXLuw9Lm6xVBK8ZvpD5d5olGQdc+NgsqjFnAHZUE+OENgY4kVU9wB84+POrI4MkoD4iHJ5a1QF8AZkZDFo1m1h9Bl+J2Ohr6MkBZq8DG5iVaunHfxUdHou5GL7lS1H7r+8ctfDXi8AfOPjzqyODJQ74Aiel35TKTOWG8pq1WO6yzJ1GNmMuMWZBamlGXoG/imnjwHY9HQtQzpGfcm0cR8X2Fd1ngNFGLDGZlWOX0jWtOwU6XVGT37JFD9W/cx4kzI+mPNi65X5WFPYlDG9N0Lbh5nOj3u3DXqRCiKCUrsEkMt8z9fxO9pLLGVQUKIYR2wTw53CiWK96FOpPevDWtH2XR0QkfOd02D73n81x6hEMCy0s3hRLn08Th9FlNHDMJBqLj+Tz8rG2TtNki3mJC7Ass1MT2qnKBI77n6vsQkAp59TfbZm/tBXwAoYdLJXge8F/numhd5AvQ+6I8ZHGJfdN3qWndvJ2I7s5Aeuzb8t9//eNsm73fIa05XreFsNyfOq1vG2COftC6EEsoJWe5h5Nwu1x6PIKuCaWxLY+npfWgM0dwJPmSgPx7TNM31LyVNS65m83pQ+qMTRH6GRVfg7HAcS5fnS/cjdbgHxEkRmgkRq1Qs48sbX9QC8nOTD0ntb6FcJyEOEOVzmJtDqimkzDq+SXR1/63AYe4LEj+ogRgN+Z8HAFhGFzd/m6snVviELfRqJ4LLQIk9Y/fzqnsF6I5OGxfdT2sxxK2Vokpi3jWhCcEknw7dYlHYpOnCHZO7QVgjQTngF2mzKf4GeOF4ECFsWTgLy6HFEitfauYJt1Xh1NfZZerBMwXLFzdhzoTQxGlcXc8lZIoEG1BLYv/ScICf8Ft9PEtpEa+j0cDSlU99UoH2xknwR1W9MRGc5I/euE63/IMJTqguZ3YcnJpjSVnAGSpyz/0gKjypJ3L86rHFRGXt0QbmaXtSl2UmmjI0p0LCCdx7McatCFEVI6FwPpPV0ZSMv/jM75eBid1X/lTV4XNzjowzR/iFlKYMzHZtVO9hCBPKlTwblRXNn4MlvNm/XeSRQ+Mr0YV5w5CL5Z/tGyzqnaLPj/kOVdyfj8r2m5Bcrz4g/ieUIo8qRFv2T2mET46ydqaxi27G4ZYHj7hbiaIqTOxWaE07qMCkJw==",
        "Status": "NEW"
    },
  "CartItems": [
    {
      "ProductName": "Mouse",
      "UnitPrice": "12000",
      "MerchantItemId": "4",
      "Sku": "abc123",
      "Quantity": 1,
      "OriginalPrice": "12000",
      "GiftMessage": "Te amo!",
      "Description": "Uma description do Mouse",
      "ShippingInstructions": "Proximo ao 546",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "123456",
      "Passenger": {
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "PassengerId": "1",
        "Status": "NEW",
        "PassengerType": "Adult",
        "Email": "emailpassageiro@dominio.com.br",
        "Phone" : "552121114700",
        "DateOfBirth": "1982-04-30"
      }
    },
    {
      "ProductName": "Teclado",
      "UnitPrice": "96385",
      "MerchantItemId": "3",
      "Sku": "abc456",
      "Quantity": 1,
      "OriginalPrice": "96385",
      "GiftMessage": "Te odeio!",
      "Description": "Uma description do Teclado",
      "ShippingInstructions": "Proximo ao 123",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "987654"
    }
  ],
  "CustomConfiguration": {
    "MerchantWebsite": "www.test.com"
  },
  "MerchantDefinedData": [
    {
      "Key": "USER_DATA4",
      "Value": "Valor definido com o Provedor a ser enviado neste campo."
    },
    {
      "Key": "Segment",
      "Value": "8999"
    },
    {
      "Key": "MerchantId",
      "Value": "Seller123456"
    }
  ],
  "Travel": {
    "CompleteRoute": "GIG-CGH-EZE",
    "DepartueTime": "2016-12-10",
    "JourneyType": "OneWayTrip",
    "TravelLegs": [
      {
        "Origin": "GIG",
        "Destination": "CGH"
      },
      {
        "Origin": "CGH",
        "Destination": "EZE"
      }
    ]
  }
}
```

**RESPONSE:**  

- Quando a transação tiver a análise realizada.  

     **Id**  
     Id da transação no Antifraude Gateway Braspag.  

     **AnalysisResult.Status**  
     Status da transação no Antifraude Gateway Braspag após a análise.  
     Enum:  
     Started = Transação recebida pela Braspag.  
     Accept = Transação aceita após análise de fraude.  
     Review = Transação em revisão após análise de fraude.  
     Reject = Transação rejeitada após análise de fraude.  
     Unfinished = Transação não finalizada por algum erro interno no sistema.  
     ProviderError = Transação com erro no provedor de antifraude.  

     **Message**  
     Mensagem de retorno do provedor de antifraude.  

     **ProviderCode**  
     Código de retorno do provedor de antifraude.  

     **ProviderTransactionId**  
     Id da transação no provedor de antifraude.  

     **ProviderRequestTransactionId**  
     Id do request da transação no provedor de antifraude.  

``` http
HTTP/1.1 201 Created
Content-Type: application/json;charset=UTF-8
```
``` json
{
  "Id": "22b5e829-edf1-e611-9414-0050569318a7",
  "AnalysisResult": {
    "Status": "Review",
    "Message": "Payment void and transaction challenged by ReD Shield",
    "ProviderCode": "100.400.148",
    "ProviderTransactionId": "487931363026",
    "ProviderRequestTransactionId": "8a8394865a353cc4015a37947c5f7e35"
  },
  "MerchantOrderId": "4493d42c-8732-4b13-aadc-b07e89732c26",
  "TotalOrderAmount": 1500,
  "TransactionAmount": 1000,
  "Currency": "BRL",
  "Provider": "RedShield",
  "OrderDate": "2016-12-09 12:35:58.852",
  "BraspagTransactionId":"a3e08eb2-2144-4e41-85d4-61f1befc7a3b",
  "SplitingPaymentMethod": "None",
  "IsRetryTransaction": false,
  "Card": {
  "Number" : "400011******0112",
    "Holder": "Fernando Test",
    "ExpirationDate": "12/2023",
    "Cvv": "***",
    "Brand": "VISA",
    "EciThreeDSecure": "5"
  },
  "Billing": {
    "Street": "Av Marechal Camara",
    "Number": "12345",
    "Complement": "Sala 123",
    "Neighborhood": "Centro",
    "City": "Rio de Janeiro",
    "State": "RJ",
    "Country": "BR",
    "ZipCode": "20080123"
  },
  "Shipping": {
        "Street": "Avenida Rio Branco",
        "Number": "30000",
        "Complement": "sl 123",
        "Neighborhood": "Centro",
        "City": "Rio de Janeiro",
        "State": "RJ",
        "Country": "BR",
        "ZipCode": "123456789",
        "Email": "emailentrega@dominio.com.br",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silvao",
        "ShippingMethod": "SameDay",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Comment": "Em frente ao 322"
    },
  "Customer": {
        "MerchantCustomerId": "10050665740",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "BirthDate": "2016-12-09",
        "Gender": "Male",
        "Email": "emailcomprador@dominio.com.br",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Ip": "127.0.0.1",
        "BrowserFingerprint": "04003hQUMXGB0poNf94lis1ztuLYRFk+zJ17aP79a9O8mWOBmEnKs6ziAo94ggAtBvKEN6/FI8Vv2QMAyHLnc295s0Nn8akZzRJtHwsEilYx1P+NzuNQnyK6+7x2OpjJZkl4NlfPt7h9d96X/miNlYT65UIY2PeH7sUAh9vKxMn1nlPu2MJCSi12NBBoiZbfxP1Whlz5wlRFwWJi0FRulruXQQGCQaJkXU7GWWZGI8Ypycnf7F299GIR12G/cdkIMFbm6Yf0/pTJUUz1vNp0X2Zw8QydKgnOIDKXq4HnEqNOos1c6njJgQh/4vXJiqy0MXMQOThNipDmXv9I185O+yC2f3lLEO0Tay66NZEyiLNePemJKSIdwO9O5ZtntuUkG6NTqARuHStXXfwp8cyGF4MPWLuvNvEfRkJupBy3Z8hSEMEK7ZWd2T2HOihQxRh4qp+NANqYKBTl3v6fQJAEKikeSQVeBN8sQqAL0BZFaIMzbrnMivi6m6JRQUIdvEt+MbJEPFc0LjRycC5ApUmJO+Aoo9VKL1B8ftMSQ1iq1uTKn16ZOmDpzZrZhMPbH83aV0rfB2GDXcjpghm9klVFOw7EoYzV7IDBIIRtgqG9KZ+8NH/z6D+YNUMLEUuK1N2ddqKbS5cKs2hplVRjwSv7x8lMXWE7VDaOZWB8+sD1cMLQtEUC0znzxZ4bpRaiSy4dJLxuJpQYAFUrDlfSKRv/eHV3QiboXLuw9Lm6xVBK8ZvpD5d5olGQdc+NgsqjFnAHZUE+OENgY4kVU9wB84+POrI4MkoD4iHJ5a1QF8AZkZDFo1m1h9Bl+J2Ohr6MkBZq8DG5iVaunHfxUdHou5GL7lS1H7r+8ctfDXi8AfOPjzqyODJQ74Aiel35TKTOWG8pq1WO6yzJ1GNmMuMWZBamlGXoG/imnjwHY9HQtQzpGfcm0cR8X2Fd1ngNFGLDGZlWOX0jWtOwU6XVGT37JFD9W/cx4kzI+mPNi65X5WFPYlDG9N0Lbh5nOj3u3DXqRCiKCUrsEkMt8z9fxO9pLLGVQUKIYR2wTw53CiWK96FOpPevDWtH2XR0QkfOd02D73n81x6hEMCy0s3hRLn08Th9FlNHDMJBqLj+Tz8rG2TtNki3mJC7Ass1MT2qnKBI77n6vsQkAp59TfbZm/tBXwAoYdLJXge8F/numhd5AvQ+6I8ZHGJfdN3qWndvJ2I7s5Aeuzb8t9//eNsm73fIa05XreFsNyfOq1vG2COftC6EEsoJWe5h5Nwu1x6PIKuCaWxLY+npfWgM0dwJPmSgPx7TNM31LyVNS65m83pQ+qMTRH6GRVfg7HAcS5fnS/cjdbgHxEkRmgkRq1Qs48sbX9QC8nOTD0ntb6FcJyEOEOVzmJtDqimkzDq+SXR1/63AYe4LEj+ogRgN+Z8HAFhGFzd/m6snVviELfRqJ4LLQIk9Y/fzqnsF6I5OGxfdT2sxxK2Vokpi3jWhCcEknw7dYlHYpOnCHZO7QVgjQTngF2mzKf4GeOF4ECFsWTgLy6HFEitfauYJt1Xh1NfZZerBMwXLFzdhzoTQxGlcXc8lZIoEG1BLYv/ScICf8Ft9PEtpEa+j0cDSlU99UoH2xknwR1W9MRGc5I/euE63/IMJTqguZ3YcnJpjSVnAGSpyz/0gKjypJ3L86rHFRGXt0QbmaXtSl2UmmjI0p0LCCdx7McatCFEVI6FwPpPV0ZSMv/jM75eBid1X/lTV4XNzjowzR/iFlKYMzHZtVO9hCBPKlTwblRXNn4MlvNm/XeSRQ+Mr0YV5w5CL5Z/tGyzqnaLPj/kOVdyfj8r2m5Bcrz4g/ieUIo8qRFv2T2mET46ydqaxi27G4ZYHj7hbiaIqTOxWaE07qMCkJw==",
        "Status": "NEW"
    },
  "CartItems": [
    {
      "ProductName": "Mouse",
      "UnitPrice": "12000",
      "MerchantItemId": "4",
      "Sku": "abc123",
      "Quantity": 1,
      "OriginalPrice": "12000",
      "GiftMessage": "Te amo!",
      "Description": "Uma description do Mouse",
      "ShippingInstructions": "Proximo ao 546",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "123456",
      "Passenger": {
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "PassengerId": "1",
        "Status": "NEW",
        "PassengerType": "Adult",
        "Email": "emailpassageiro@dominio.com.br",
        "Phone" : "552121114700",
        "DateOfBirth": "1982-04-30"
      }
    },
    {
      "ProductName": "Teclado",
      "UnitPrice": "96385",
      "MerchantItemId": "3",
      "Sku": "abc456",
      "Quantity": 1,
      "OriginalPrice": "96385",
      "GiftMessage": "Te odeio!",
      "Description": "Uma description do Teclado",
      "ShippingInstructions": "Proximo ao 123",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "987654"
    }
  ],
  "CustomConfiguration": {
    "MerchantWebsite": "www.test.com"
  },
  "MerchantDefinedData": [
    {
      "Key": "USER_DATA4",
      "Value": "Valor definido com o Provedor a ser enviado neste campo."
    },
    {
      "Key": "Segment",
      "Value": "8999"
    },
    {
      "Key": "MerchantId",
      "Value": "Seller123456"
    }
  ],
  "Travel": {
    "CompleteRoute": "GIG-CGH-EZE",
    "DepartueTime": "2016-12-10",
    "JourneyType": "OneWayTrip",
    "TravelLegs": [
      {
        "Origin": "GIG",
        "Destination": "CGH"
      },
      {
        "Origin": "CGH",
        "Destination": "EZE"
      }
    ]
  }
}
```
- Quando os dados enviados para análise tiver alguma inconformidade nos valores, tamanhos permitidos e/ou tipos dos campos conforme especificação do manual.  

     **Message**  
     Mensagem para um requisição inválida.  

     **ModelState**  
     Caso algum campo não esteja de acordo com o tipo ou domínio especificado no manual.  

     **ModelState.FraudAnalysisRequestError**  
     Caso algum campo não esteja de acordo com o tamanho especificado no manual.  

``` http
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=UTF-8
```
``` json
{
  "Message": "The request is invalid.",
  "ModelState": {
    "request.Customer.Gender": [
      "Error converting value \"M\" to type 'Antifraude.Domain.Enums.GenderType'. Path 'Customer.Gender', line 51, position 17."
    ],
    "FraudAnalysisRequestError": [
      "The Card.EciThreeDSecure lenght is gratter than 1",
      "The Shipping.Complement lenght is gratter than 14",
      "The Shipping.MiddleName lenght is gratter than 1",
      "The Customer.MerchantCustomerId lenght is gratter than 16",
      "The Customer.MiddleName lenght is gratter than 1"
    ]
  }
}
```
  
<a style="float: right;" href="#http_operations"><i class="fa fa-angle-double-up fa-fw"></i></a>
  
<a name="get_analise"></a>

#### `GET`{:.http-get} Obtenção dos Detalhes da Análise
-------------------------------------------------

**PARÂMETROS:**  

``` csharp
Id: Guid  // Id da Transação no Antifraude
```

**REQUEST:**  

``` http
GET https://riskhomolog.braspag.com.br/Analysis/{Id} HTTP/1.1
Host: riskhomolog.braspag.com.br
Authorization: Bearer {access_token}
Content-Type: application/json
```

**RESPONSE:**  

- Quando a transação não for encontrada na base de dados.  

``` http
HTTP/1.1 404 Not Found
Content-Type: application/json;charset=UTF-8
```

- Quando a transação for encontrada na base de dados.  

``` http
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
```
``` json
{
  "Id": "22b5e829-edf1-e611-9414-0050569318a7",
  "AnalysisResult": {
    "Status": "Review",
    "Message": "Payment void and transaction challenged by ReD Shield",
    "ProviderCode": "100.400.148",
    "ProviderTransactionId": "487931363026",
    "ProviderRequestTransactionId": "8a8394865a353cc4015a37947c5f7e35"
  },
  "MerchantOrderId": "4493d42c-8732-4b13-aadc-b07e89732c26",
  "TotalOrderAmount": 1500,
  "TransactionAmount": 1000,
  "Currency": "BRL",
  "Provider": "RedShield",
  "OrderDate": "2016-12-09 12:35:58.852",
  "BraspagTransactionId":"a3e08eb2-2144-4e41-85d4-61f1befc7a3b",
  "SplitingPaymentMethod": "None",
  "IsRetryTransaction": false,
  "Card": {
  "Number" : "400011******0112",
    "Holder": "Fernando Test",
    "ExpirationDate": "12/2023",
    "Cvv": "***",
    "Brand": "VISA",
    "EciThreeDSecure": "5"
  },
  "Billing": {
    "Street": "Av Marechal Camara",
    "Number": "12345",
    "Complement": "Sala 123",
    "Neighborhood": "Centro",
    "City": "Rio de Janeiro",
    "State": "RJ",
    "Country": "BR",
    "ZipCode": "20080123"
  },
  "Shipping": {
        "Street": "Avenida Rio Branco",
        "Number": "30000",
        "Complement": "sl 123",
        "Neighborhood": "Centro",
        "City": "Rio de Janeiro",
        "State": "RJ",
        "Country": "BR",
        "ZipCode": "123456789",
        "Email": "emailentrega@dominio.com.br",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silvao",
        "ShippingMethod": "SameDay",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Comment": "Em frente ao 322"
    },
  "Customer": {
        "MerchantCustomerId": "10050665740",
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "BirthDate": "2016-12-09",
        "Gender": "Male",
        "Email": "emailcomprador@dominio.com.br",
        "Phone": "552121114700",
        "WorkPhone": "552121114721",
        "Mobile": "5521998765432",
        "Ip": "127.0.0.1",
        "BrowserFingerprint": "04003hQUMXGB0poNf94lis1ztuLYRFk+zJ17aP79a9O8mWOBmEnKs6ziAo94ggAtBvKEN6/FI8Vv2QMAyHLnc295s0Nn8akZzRJtHwsEilYx1P+NzuNQnyK6+7x2OpjJZkl4NlfPt7h9d96X/miNlYT65UIY2PeH7sUAh9vKxMn1nlPu2MJCSi12NBBoiZbfxP1Whlz5wlRFwWJi0FRulruXQQGCQaJkXU7GWWZGI8Ypycnf7F299GIR12G/cdkIMFbm6Yf0/pTJUUz1vNp0X2Zw8QydKgnOIDKXq4HnEqNOos1c6njJgQh/4vXJiqy0MXMQOThNipDmXv9I185O+yC2f3lLEO0Tay66NZEyiLNePemJKSIdwO9O5ZtntuUkG6NTqARuHStXXfwp8cyGF4MPWLuvNvEfRkJupBy3Z8hSEMEK7ZWd2T2HOihQxRh4qp+NANqYKBTl3v6fQJAEKikeSQVeBN8sQqAL0BZFaIMzbrnMivi6m6JRQUIdvEt+MbJEPFc0LjRycC5ApUmJO+Aoo9VKL1B8ftMSQ1iq1uTKn16ZOmDpzZrZhMPbH83aV0rfB2GDXcjpghm9klVFOw7EoYzV7IDBIIRtgqG9KZ+8NH/z6D+YNUMLEUuK1N2ddqKbS5cKs2hplVRjwSv7x8lMXWE7VDaOZWB8+sD1cMLQtEUC0znzxZ4bpRaiSy4dJLxuJpQYAFUrDlfSKRv/eHV3QiboXLuw9Lm6xVBK8ZvpD5d5olGQdc+NgsqjFnAHZUE+OENgY4kVU9wB84+POrI4MkoD4iHJ5a1QF8AZkZDFo1m1h9Bl+J2Ohr6MkBZq8DG5iVaunHfxUdHou5GL7lS1H7r+8ctfDXi8AfOPjzqyODJQ74Aiel35TKTOWG8pq1WO6yzJ1GNmMuMWZBamlGXoG/imnjwHY9HQtQzpGfcm0cR8X2Fd1ngNFGLDGZlWOX0jWtOwU6XVGT37JFD9W/cx4kzI+mPNi65X5WFPYlDG9N0Lbh5nOj3u3DXqRCiKCUrsEkMt8z9fxO9pLLGVQUKIYR2wTw53CiWK96FOpPevDWtH2XR0QkfOd02D73n81x6hEMCy0s3hRLn08Th9FlNHDMJBqLj+Tz8rG2TtNki3mJC7Ass1MT2qnKBI77n6vsQkAp59TfbZm/tBXwAoYdLJXge8F/numhd5AvQ+6I8ZHGJfdN3qWndvJ2I7s5Aeuzb8t9//eNsm73fIa05XreFsNyfOq1vG2COftC6EEsoJWe5h5Nwu1x6PIKuCaWxLY+npfWgM0dwJPmSgPx7TNM31LyVNS65m83pQ+qMTRH6GRVfg7HAcS5fnS/cjdbgHxEkRmgkRq1Qs48sbX9QC8nOTD0ntb6FcJyEOEOVzmJtDqimkzDq+SXR1/63AYe4LEj+ogRgN+Z8HAFhGFzd/m6snVviELfRqJ4LLQIk9Y/fzqnsF6I5OGxfdT2sxxK2Vokpi3jWhCcEknw7dYlHYpOnCHZO7QVgjQTngF2mzKf4GeOF4ECFsWTgLy6HFEitfauYJt1Xh1NfZZerBMwXLFzdhzoTQxGlcXc8lZIoEG1BLYv/ScICf8Ft9PEtpEa+j0cDSlU99UoH2xknwR1W9MRGc5I/euE63/IMJTqguZ3YcnJpjSVnAGSpyz/0gKjypJ3L86rHFRGXt0QbmaXtSl2UmmjI0p0LCCdx7McatCFEVI6FwPpPV0ZSMv/jM75eBid1X/lTV4XNzjowzR/iFlKYMzHZtVO9hCBPKlTwblRXNn4MlvNm/XeSRQ+Mr0YV5w5CL5Z/tGyzqnaLPj/kOVdyfj8r2m5Bcrz4g/ieUIo8qRFv2T2mET46ydqaxi27G4ZYHj7hbiaIqTOxWaE07qMCkJw==",
        "Status": "NEW"
    },
  "CartItems": [
    {
      "ProductName": "Mouse",
      "UnitPrice": "12000",
      "MerchantItemId": "4",
      "Sku": "abc123",
      "Quantity": 1,
      "OriginalPrice": "12000",
      "GiftMessage": "Te amo!",
      "Description": "Uma description do Mouse",
      "ShippingInstructions": "Proximo ao 546",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "123456",
      "Passenger": {
        "FirstName": "João",
        "MiddleName": "P",
        "LastName": "Silva",
        "PassengerId": "1",
        "Status": "NEW",
        "PassengerType": "Adult",
        "Email": "emailpassageiro@dominio.com.br",
        "Phone" : "552121114700",
        "DateOfBirth": "1982-04-30"
      }
    },
    {
      "ProductName": "Teclado",
      "UnitPrice": "96385",
      "MerchantItemId": "3",
      "Sku": "abc456",
      "Quantity": 1,
      "OriginalPrice": "96385",
      "GiftMessage": "Te odeio!",
      "Description": "Uma description do Teclado",
      "ShippingInstructions": "Proximo ao 123",
      "ShippingMethod": "SameDay",
      "ShippingTrackingNumber": "987654"
    }
  ],
  "CustomConfiguration": {
    "MerchantWebsite": "www.test.com"
  },
  "MerchantDefinedData": [
    {
      "Key": "USER_DATA4",
      "Value": "Valor definido com o Provedor a ser enviado neste campo."
    },
    {
      "Key": "Segment",
      "Value": "8999"
    },
    {
      "Key": "MerchantId",
      "Value": "Seller123456"
    }
  ],
  "Travel": {
    "CompleteRoute": "GIG-CGH-EZE",
    "DepartueTime": "2016-12-10",
    "JourneyType": "OneWayTrip",
    "TravelLegs": [
      {
        "Origin": "GIG",
        "Destination": "CGH"
      },
      {
        "Origin": "CGH",
        "Destination": "EZE"
      }
    ]
  }
}
```
  
<a style="float: right;" href="#http_operations"><i class="fa fa-angle-double-up fa-fw"></i></a>
  

