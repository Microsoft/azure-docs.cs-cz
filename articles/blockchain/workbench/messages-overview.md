---
title: Použití zpráv pro integraci s Azure blockchain Workbench
description: Přehled používání zpráv pro integraci Azure blockchain Workbench Preview s ostatními systémy.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74324509"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrace zasílání zpráv v Azure blockchain Workbench

Kromě poskytování REST API poskytuje Azure blockchain Workbench taky integraci na základě zpráv. Aplikace Workbench publikuje události orientované na hlavní knihu prostřednictvím Azure Event Grid, což umožní uživatelům pro příjem dat ingestovat data nebo provádět akce na základě těchto událostí. Pro ty klienty, kteří vyžadují spolehlivé zasílání zpráv, služba Azure blockchain Workbench doručuje i zprávy na koncový bod Azure Service Bus.

## <a name="input-apis"></a>Vstupní rozhraní API

Pokud chcete iniciovat transakce z externích systémů pro vytváření uživatelů, vytvářet kontrakty a aktualizovat smlouvy, můžete k provádění transakcí v hlavní knize použít vstupní rozhraní API pro zasílání zpráv. Ukázku, která předvádí vstupní rozhraní API, najdete v tématu [ukázky integrace zasílání zpráv](https://aka.ms/blockchain-workbench-integration-sample) .

K dispozici jsou následující vstupní rozhraní API.

### <a name="create-user"></a>Vytvořit uživatele

Vytvoří nového uživatele.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                      |
|----------------------|------------------------------------------------------|
| Identifikátor            | Identifikátor GUID zadaného klienta                                |
| firstName            | Křestní jméno uživatele                              |
| lastName             | Příjmení uživatele                               |
| emailAddress         | E-mailová adresa uživatele                           |
| externalId           | ID objektu Azure AD uživatele                      |
| connectionId         | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion | Verze schématu zasílání zpráv                            |
| Zpráva          | **CreateUserRequest**                               |

Příklad:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench vrátí odpověď s následujícími poli:

| **Název**              | **Popis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Identifikátor             | Identifikátor GUID zadaného klienta |
| userId                | ID vytvořeného uživatele |
| userChainIdentifier   | Adresa uživatele, který byl vytvořen v blockchain síti. V Ethereem je adresa adresa uživatele **v řetězci** . |
| connectionId          | Jedinečný identifikátor pro připojení blockchain|
| messageSchemaVersion  | Verze schématu zasílání zpráv |
| Zpráva           | **CreateUserUpdate** |
| status                | Stav žádosti o vytvoření uživatele  V případě úspěchu je hodnota **úspěšná**. Při selhání je hodnota **selhání**.     |
| additionalInformation | Další informace poskytované na základě stavu |

Příklad úspěšného **Vytvoření odpovědi uživatele** z blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Pokud se žádost nezdařila, podrobnosti o selhání jsou uvedeny v části Další informace.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Vytvořit kontrakt

Vytvoří novou kontrakt.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Identifikátor            | Identifikátor GUID zadaného klienta |
| userChainIdentifier  | Adresa uživatele, který byl vytvořen v blockchain síti. V Ethereem je tato adresa adresou **řetězce** uživatele. |
| applicationName      | Název aplikace |
| verze              | Verze aplikace Vyžaduje se, pokud máte povoleno více verzí aplikace. V opačném případě je verze volitelná. Další informace o používání verzí aplikací najdete v tématu [Správa verzí aplikací v Azure blockchain Workbench](version-app.md). |
| workflowName         | Název pracovního postupu |
| parameters           | Vstupní parametry pro vytvoření kontraktu |
| connectionId         | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| Zpráva          | **CreateContractRequest** |

Příklad:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench vrátí odpověď s následujícími poli:

| **Název**                 | **Popis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Identifikátor                | Identifikátor GUID zadaného klienta                                                             |
| contractId               | Jedinečný identifikátor kontraktu v rámci Azure blockchain Workbench |
| contractLedgerIdentifier | Adresa smlouvy v hlavní knize                                            |
| connectionId             | Jedinečný identifikátor pro připojení blockchain                               |
| messageSchemaVersion     | Verze schématu zasílání zpráv                                                         |
| Zpráva              | **CreateContractUpdate**                                                      |
| status                   | Stav požadavku na vytvoření kontraktu  Možné hodnoty: **odesláno**, **potvrzeno**, **selhání**.  |
| additionalInformation    | Další informace poskytované na základě stavu                              |

Příklad odpovědi odeslaných **kontraktů Create** z blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Příklad potvrzené odpovědi **kontraktu Create** z blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Pokud se žádost nezdařila, podrobnosti o selhání jsou uvedeny v části Další informace.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Vytvořit akci kontraktu

Vytvoří novou akci kontraktu.

Požadavek vyžaduje následující pole:

| **Název**                 | **Popis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Identifikátor                | Identifikátor GUID zadaného klienta |
| userChainIdentifier      | Adresa uživatele, který byl vytvořen v blockchain síti. V Ethereem je tato adresa adresou **řetězce** uživatele. |
| contractLedgerIdentifier | Adresa smlouvy v hlavní knize |
| verze                  | Verze aplikace Vyžaduje se, pokud máte povoleno více verzí aplikace. V opačném případě je verze volitelná. Další informace o používání verzí aplikací najdete v tématu [Správa verzí aplikací v Azure blockchain Workbench](version-app.md). |
| workflowFunctionName     | Název funkce pracovního postupu |
| parameters               | Vstupní parametry pro vytvoření kontraktu |
| connectionId             | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion     | Verze schématu zasílání zpráv |
| Zpráva              | **CreateContractActionRequest** |

Příklad:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench vrátí odpověď s následujícími poli:

| **Název**              | **Popis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Identifikátor             | Identifikátor GUID zadaného klienta|
| contractId            | Jedinečný identifikátor kontraktu v rámci Azure blockchain Workbench |
| connectionId          | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion  | Verze schématu zasílání zpráv |
| Zpráva           | **CreateContractActionUpdate** |
| status                | Stav požadavku akce kontraktu Možné hodnoty: **odesláno**, **potvrzeno**, **selhání**.                         |
| additionalInformation | Další informace poskytované na základě stavu |

Příklad odeslané reakce **akce vytvoření kontraktu** z blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Příklad potvrzené reakce **akce vytvoření kontraktu** z blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Pokud se žádost nezdařila, podrobnosti o selhání jsou uvedeny v části Další informace.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Vstupní kódy a zprávy o chybách rozhraní API

**Kód chyby 4000: Chybná chyba žádosti**
- Neplatný connectionId
- Deserializace CreateUserRequest se nezdařila
- Deserializace CreateContractRequest se nezdařila
- Deserializace CreateContractActionRequest se nezdařila
- Aplikace {identifikovaná názvem aplikace} neexistuje.
- Aplikace {identifikovaná názvem aplikace} nemá pracovní postup.
- UserChainIdentifier neexistuje.
- Kontrakt {identifikovaný identifikátorem hlavní knihy} neexistuje.
- Kontrakt {identifikovaný identifikátorem hlavní knihy} nemá funkci {název funkce pracovního postupu}.
- UserChainIdentifier neexistuje.

**Kód chyby 4090: Chyba konfliktu**
- Uživatel už existuje.
- Kontrakt již existuje.
- Akce kontraktu už existuje.

**Kód chyby 5000: interní chyba serveru**
- Zprávy výjimek

## <a name="event-notifications"></a>Oznámení událostí

Oznámení o událostech se dají použít k oznámení uživatelům a navazujícím systémům událostí, ke kterým dochází v blockchain Workbench a síti blockchain, ke které se připojuje. Oznámení událostí lze spotřebovat přímo v kódu nebo použít s nástroji, jako je Logic Apps a flow, pro aktivaci toku dat do navazujících systémů.

Podrobnosti o různých zprávách, které se dají přijmout, najdete v tématu Referenční dokumentace k [oznamovací zprávě](#notification-message-reference) .

### <a name="consuming-event-grid-events-with-azure-functions"></a>Využívání Event Gridch událostí s Azure Functions

Pokud chce uživatel použít Event Grid k oznámení o událostech, ke kterým dochází v blockchain Workbench, můžete zpracovávat události z Event Grid pomocí Azure Functions.

1. Vytvořte v Azure Portal **Function App Azure** .
2. Vytvoří novou funkci.
3. Vyhledejte šablonu pro Event Grid. Zobrazí se základní kód šablony pro čtení zprávy. Upravte kód podle potřeby.
4. Uložte funkci. 
5. Vyberte Event Grid ze skupiny prostředků blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Využívání Event Gridch událostí s Logic Apps

1. Vytvořte novou **aplikaci logiky Azure** v Azure Portal.
2. Při otevírání aplikace logiky Azure na portálu se zobrazí výzva k výběru triggeru. Vyberte **Azure Event Grid – když dojde k události prostředku**.
3. Po zobrazení návrháře pracovních postupů se zobrazí výzva, abyste se přihlásili.
4. Vyberte předplatné. Prostředek jako **Microsoft. EventGrid. témata**. Vyberte **název prostředku** z názvu prostředku ze skupiny prostředků Azure blockchain Workbench.
5. Vyberte Event Grid ze skupiny prostředků blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Použití témat Service Bus pro oznámení

Service Bus témata lze použít k upozornění uživatelů na události, ke kterým dochází v blockchain Workbench. 

1. Přejděte do Service Bus v rámci skupiny prostředků Workbench.
2. Vyberte **Témata**.
3. Vyberte **výstup – téma**.
4. Vytvoří nové předplatné tohoto tématu. Získejte pro něj klíč.
5. Vytvořte program, který se přihlásí k odběru událostí z tohoto předplatného.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Využívání zpráv Service Bus s Logic Apps

1. Vytvořte novou **aplikaci logiky Azure** v Azure Portal.
2. Při otevírání aplikace logiky Azure na portálu se zobrazí výzva k výběru triggeru. Do vyhledávacího pole zadejte **Service Bus** a vyberte aktivační událost, která je vhodná pro typ interakce, kterou chcete s Service Bus. Například **Service Bus – při přijetí zprávy v odběru tématu (automatické dokončení)**.
3. Po zobrazení návrháře pracovních postupů zadejte informace o připojení pro Service Bus.
4. Vyberte své předplatné a určete téma **Workbench-External**.
5. Vytvořte logiku pro aplikaci, která využívá zprávu z této aktivační události.

## <a name="notification-message-reference"></a>Odkaz na zprávu oznámení

V závislosti na tom **, že zprávy s** oznámením mají jeden z následujících typů zpráv.

### <a name="block-message"></a>Blokovat zprávu

Obsahuje informace o jednotlivých blocích. *BlockMessage* obsahuje oddíl s informacemi na úrovni bloku a oddíl s informacemi o transakcích.

| Název | Description |
|------|-------------|
| blokovat | Obsahuje [informace o bloku](#block-information) |
| transactions | Obsahuje [informace o transakci](#transaction-information) kolekce pro blok. |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| Zpráva | **BlockMessage** |
| additionalInformation | Další poskytnuté informace |

#### <a name="block-information"></a>Informace o blokování

| Název              | Description |
|-------------------|-------------|
| blockId           | Jedinečný identifikátor pro blok uvnitř Azure blockchain Workbench |
| blockNumber       | Jedinečný identifikátor bloku v hlavní knize |
| blockHash         | Hodnota hash bloku |
| previousBlockHash | Hodnota hash předchozího bloku |
| blockTimestamp    | Časové razítko bloku |

#### <a name="transaction-information"></a>Informace o transakcích

| Název               | Description |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor transakce v rámci Azure blockchain Workbench |
| transactionHash    | Hodnota hash transakce v hlavní knize |
| Výsledkem               | Jedinečný identifikátor v hlavní knize pro zdroj transakce |
| na                 | Jedinečný identifikátor v hlavní knize pro cíl transakce |
| provisioningStatus | Určuje aktuální stav procesu zřizování pro transakci. Možné hodnoty: </br>0 – transakce byla vytvořena rozhraním API v databázi.</br>1 – transakce byla odeslána do hlavní knihy.</br>2 – transakce byla úspěšně potvrzena do hlavní knihy.</br>3 nebo 4 – transakci se nepovedlo zapsat do hlavní knihy.</br>5 – transakce byla úspěšně potvrzena do hlavní knihy. |

Příklad *BlockMessage* z blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Zpráva smlouvy

Obsahuje informace o kontraktu. Zpráva obsahuje oddíl s vlastnostmi kontraktu a oddílem s informacemi o transakcích. Všechny transakce, které změnily kontrakt pro konkrétní blok, jsou zahrnuty v části transakce.

| Název | Description |
|------|-------------|
| blockId | Jedinečný identifikátor pro blok uvnitř Azure blockchain Workbench |
| blockHash | Hodnota hash bloku |
| modifyingTransactions | [Transakce, které změnily](#modifying-transaction-information) kontrakt |
| contractId | Jedinečný identifikátor kontraktu v rámci Azure blockchain Workbench |
| contractLedgerIdentifier | Jedinečný identifikátor pro kontrakt v hlavní knize |
| contractProperties | [Vlastnosti kontraktu](#contract-properties) |
| isNewContract | Označuje, zda byla tato smlouva nově vytvořena. Možné hodnoty jsou: true: Tato smlouva byla vytvořena novou smlouvou. false: Tato smlouva je aktualizace smlouvy. |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| Zpráva | **ContractMessage** |
| additionalInformation | Další poskytnuté informace |

#### <a name="modifying-transaction-information"></a>Úprava informací o transakci

| Název               | Description |
|--------------------|-------------|
| transactionId | Jedinečný identifikátor transakce v rámci Azure blockchain Workbench |
| transactionHash | Hodnota hash transakce v hlavní knize |
| Výsledkem | Jedinečný identifikátor v hlavní knize pro zdroj transakce |
| na | Jedinečný identifikátor v hlavní knize pro cíl transakce |

#### <a name="contract-properties"></a>Vlastnosti kontraktu

| Název               | Description |
|--------------------|-------------|
| workflowPropertyId | Jedinečný identifikátor pro vlastnost workflow v rámci Azure blockchain Workbench |
| name | Název vlastnosti pracovního postupu |
| hodnota | Hodnota vlastnosti Workflow |

Příklad *ContractMessage* z blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Zpráva události: vyvolání funkce kontraktu

Obsahuje informace o vyvolání funkce kontraktu, jako je název funkce, vstup parametrů a volající funkce.

| Název | Description |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| volající                      | [Informace o volajícím](#caller-information) |
| contractId                  | Jedinečný identifikátor kontraktu v rámci Azure blockchain Workbench |
| contractLedgerIdentifier    | Jedinečný identifikátor pro kontrakt v hlavní knize |
| functionName                | Název funkce |
| parameters                  | [Informace o parametru](#parameter-information) |
| transakce                 | Informace o transakcích |
| inTransactionSequenceNumber | Pořadové číslo transakce v bloku |
| connectionId                | Jedinečný identifikátor pro připojení |
| messageSchemaVersion        | Verze schématu zasílání zpráv |
| Zpráva                 | **EventMessage** |
| additionalInformation       | Další poskytnuté informace |

#### <a name="caller-information"></a>Informace o volajícím

| Název | Description |
|------|-------------|
| typ | Typ volajícího, jako je uživatel nebo kontrakt |
| id | Jedinečný identifikátor volajícího v rámci Azure blockchain Workbench |
| ledgerIdentifier | Jedinečný identifikátor volajícího v hlavní knize |

#### <a name="parameter-information"></a>Informace o parametru

| Název | Description |
|------|-------------|
| name | Název parametru |
| hodnota | Hodnota parametru |

#### <a name="event-message-transaction-information"></a>Informace o transakcích zprávy o událostech

| Název               | Description |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor transakce v rámci Azure blockchain Workbench |
| transactionHash    | Hodnota hash transakce v hlavní knize |
| Výsledkem               | Jedinečný identifikátor v hlavní knize pro zdroj transakce |
| na                 | Jedinečný identifikátor v hlavní knize pro cíl transakce |

Příklad *EventMessage ContractFunctionInvocation* z blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Zpráva o události: ingestování aplikací

Obsahuje informace o tom, kdy se aplikace nahrává do aplikace Workbench, jako je například název a verze nahrané aplikace.

| Název | Description |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Jedinečný identifikátor pro aplikaci v rámci Azure blockchain Workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationDefinitionLocation | Adresa URL, kde se nachází konfigurační soubor aplikace |
| contractCodes | Kolekce [kódů smluv](#contract-code-information) pro aplikaci |
| applicationRoles | Kolekce [aplikačních rolí](#application-role-information) pro aplikaci |
| applicationWorkflows | Kolekce [pracovních postupů aplikací](#application-workflow-information) pro aplikaci |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| Zpráva | **EventMessage** |
| additionalInformation | Zde jsou uvedeny další informace o stavech pracovního postupu aplikace a informace o přechodu. |

#### <a name="contract-code-information"></a>Informace o kódu kontraktu

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor souboru kódu kontraktu v Azure blockchain Workbench |
| ledgerId | Jedinečný identifikátor pro hlavní knihu v rámci Azure blockchain Workbench |
| location | Adresa URL, kde se nachází soubor kódu kontraktu |

#### <a name="application-role-information"></a>Informace o aplikační roli

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor aplikační role v rámci Azure blockchain Workbench |
| name | Název aplikační role |

#### <a name="application-workflow-information"></a>Informace o pracovním postupu aplikace

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor pracovního postupu aplikace v rámci služby Azure blockchain Workbench |
| name | Název pracovního postupu aplikace |
| displayName | Zobrazovaný název pracovního postupu aplikace |
|  – funkce | Kolekce [funkcí pro pracovní postup aplikace](#workflow-function-information)|
| státem | Kolekce [stavů pro pracovní postup aplikace](#workflow-state-information) |
| properties | [Informace o vlastnostech pracovního postupu](#workflow-property-information) aplikace |

##### <a name="workflow-function-information"></a>Informace o funkci pracovního postupu

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor funkce pracovního postupu aplikace v rámci služby Azure blockchain Workbench |
| name | Název funkce |
| parameters | Parametry pro funkci |

##### <a name="workflow-state-information"></a>Informace o stavu pracovního postupu

| Název | Description |
|------|-------------|
| name | Název stavu |
| displayName | Zobrazovaný název stavu |
| style | Styl stavu (úspěch nebo neúspěch) |

##### <a name="workflow-property-information"></a>Informace o vlastnostech pracovního postupu

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor pro vlastnost pracovního postupu aplikace v rámci služby Azure blockchain Workbench |
| name | Název vlastnosti |
| typ | Typ vlastnosti |

Příklad *EventMessage ApplicationIngestion* z blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                        "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                        "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Zpráva události: přiřazení role

Obsahuje informace o tom, kdy je uživateli přiřazena role v aplikaci Workbench, jako je například osoba, která provedla přiřazení role, a název role a odpovídající aplikace.

| Název | Description |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Jedinečný identifikátor pro aplikaci v rámci Azure blockchain Workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationRole        | Informace o [aplikační roli](#roleassignment-application-role) |
| Pověřující osoby               | Informace o [přiřazení](#roleassignment-assigner) |
| zmocněn               | Informace o [pověřené osobě](#roleassignment-assignee) |
| connectionId           | Jedinečný identifikátor pro připojení |
| messageSchemaVersion   | Verze schématu zasílání zpráv |
| Zpráva            | **EventMessage** |
| additionalInformation  | Další poskytnuté informace |

#### <a name="roleassignment-application-role"></a>Role aplikace RoleAssignment

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor aplikační role v rámci Azure blockchain Workbench |
| name | Název aplikační role |

#### <a name="roleassignment-assigner"></a>RoleAssignment přiřazení

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor uživatele v rámci Azure blockchain Workbench |
| typ | Typ přiřazení |
| chainIdentifier | Jedinečný identifikátor uživatele v hlavní knize |

#### <a name="roleassignment-assignee"></a>RoleAssignment zmocněnec

| Název | Description |
|------|-------------|
| id | Jedinečný identifikátor uživatele v rámci Azure blockchain Workbench |
| typ | Typ pověřené osoby |
| chainIdentifier | Jedinečný identifikátor uživatele v hlavní knize |

Příklad *EventMessage RoleAssignment* z blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": "1.0",
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Další kroky

- [Vzory integrace inteligentních kontraktů](integration-patterns.md)
