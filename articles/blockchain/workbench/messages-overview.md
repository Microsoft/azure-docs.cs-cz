---
title: Přehled integrace zpráv služby Azure Blockchain Workbench
description: Základní informace o použití zpráv v aplikaci Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/01/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 48e7de2798d9c34942df281febcc1d4ec443010d
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662223"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrace zasílání zpráv Azure Blockchain Workbench

Kromě rozhraní REST API, Azure Blockchain Workbench umožňuje také nabízí integrace založené na zasílání zpráv. Aplikace Workbench publikuje zaměřené na účetní knihy události prostřednictvím služby Azure Event Grid, která podřízené zákazníkům umožní ingestovat data nebo provést akce na základě těchto událostí. U těchto klientů, které vyžadují spolehlivé zasílání zpráv Azure Blockchain Workbench předává zprávy ostatním koncový Azure Service Bus.

## <a name="input-apis"></a>Vstupní rozhraní API

Pokud chcete zahájit transakce z externích systémů vytvořit uživatele, kontrakty vytvářet a aktualizovat smluv, můžete použít zasílání zpráv vstupní rozhraní API provádět transakce na účetní kniha. Zobrazit [ukázky integrace zasílání zpráv](https://aka.ms/blockchain-workbench-integration-sample) ukázku, která předvádí vstupní rozhraní API.

Níže jsou aktuálně k dispozici vstup rozhraní API.

### <a name="create-user"></a>Vytvořit uživatele

Vytvoří nového uživatele.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                      |
|----------------------|------------------------------------------------------|
| requestId            | Klientem poskytnutý identifikátor GUID                                |
| Jméno            | Křestní jméno uživatele                              |
| Příjmení             | Příjmení uživatele                               |
| emailAddress         | E-mailovou adresu uživatele                           |
| externalId           | Azure AD ID objektu uživatele                      |
| connectionId         | Jedinečný identifikátor pro připojení k blockchainu |
| messageSchemaVersion | Zasílání zpráv verze schématu                            |
| messageName          | **CreateUserRequest**                               |

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

Blockchain Workbench vrátí odpověď se následující pole:

| **Název**              | **Popis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | Klientem poskytnutý identifikátor GUID |
| userId                | ID uživatele, který byl vytvořen |
| UserChainIdentifier   | Adresa uživatele, který byl vytvořen v síti blockchain. V Etherea, je adresa uživatele **v řetězu** adresu. |
| connectionId          | Jedinečný identifikátor pro připojení k blockchainu|
| messageSchemaVersion  | Zasílání zpráv verze schématu |
| messageName           | **CreateUserUpdate** |
| status                | Stav požadavku na vytvoření uživatele.  Pokud úspěšné, je hodnota **úspěch**. Při selhání, hodnota je **selhání**.     |
| AdditionalInformation | Další informace najdete na základě stavu |

Příklad úspěšné **vytvořit uživatele** neodpověděla Blockchain Workbench:

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

Pokud požadavek nebyl úspěšný, podrobnosti o chybě jsou zahrnuty v další informace.

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

### <a name="create-contract"></a>Vytvoření kontraktu

Vytvoří nové smlouvy.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | Klientem poskytnutý identifikátor GUID |
| UserChainIdentifier  | Adresa uživatele, který byl vytvořen v síti blockchain. V Etherea, tato adresa je uživatele **v řetězu** adresu. |
| applicationName      | Název aplikace |
| version              | Verze aplikace Povinné, pokud máte více verzí aplikace povolena. V opačném případě verze je volitelné. Další informace o správy verzí aplikací, najdete v části [správy verzí aplikací Azure Blockchain Workbench](version-app.md). |
| WorkflowName         | Název pracovního postupu |
| parameters           | Vstupní parametry pro vytvoření kontraktu |
| connectionId         | Jedinečný identifikátor pro připojení k blockchainu |
| messageSchemaVersion | Zasílání zpráv verze schématu |
| messageName          | **CreateContractRequest** |

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

Blockchain Workbench vrátí odpověď se následující pole:

| **Název**                 | **Popis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | Klientem poskytnutý identifikátor GUID                                                             |
| contractId               | Jedinečný identifikátor pro kontrakt uvnitř Azure Blockchain Workbench |
| contractLedgerIdentifier | Adresa kontraktu na hlavní knihy                                            |
| connectionId             | Jedinečný identifikátor pro připojení k blockchainu                               |
| messageSchemaVersion     | Zasílání zpráv verze schématu                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Stav požadavku na vytvoření kontraktu.  Možné hodnoty: **Odeslání**, **potvrzené**, **selhání**.  |
| AdditionalInformation    | Další informace najdete na základě stavu                              |

Příklad odeslané **vytvoření kontraktu** neodpověděla Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Příklad potvrzené **vytvoření kontraktu** neodpověděla Blockchain Workbench:

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

Pokud požadavek nebyl úspěšný, podrobnosti o chybě jsou zahrnuty v další informace.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Vytvoření kontraktu akce

Vytvoří novou akci kontraktu.

Požadavek vyžaduje následující pole:

| **Název**                 | **Popis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | Klientem poskytnutý identifikátor GUID |
| UserChainIdentifier      | Adresa uživatele, který byl vytvořen v síti blockchain. V Etherea, tato adresa je uživatele **v řetězu** adresu. |
| contractLedgerIdentifier | Adresa kontraktu na hlavní knihy |
| version                  | Verze aplikace Povinné, pokud máte více verzí aplikace povolena. V opačném případě verze je volitelné. Další informace o správy verzí aplikací, najdete v části [správy verzí aplikací Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Název funkce pracovního postupu |
| parameters               | Vstupní parametry pro vytvoření kontraktu |
| connectionId             | Jedinečný identifikátor pro připojení k blockchainu |
| messageSchemaVersion     | Zasílání zpráv verze schématu |
| messageName              | **CreateContractActionRequest** |

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

Blockchain Workbench vrátí odpověď se následující pole:

| **Název**              | **Popis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | Klientem poskytnutý identifikátor GUID|
| contractId            | Jedinečný identifikátor pro kontrakt uvnitř Azure Blockchain Workbench |
| connectionId          | Jedinečný identifikátor pro připojení k blockchainu |
| messageSchemaVersion  | Zasílání zpráv verze schématu |
| messageName           | **CreateContractActionUpdate** |
| status                | Stav požadavku na akce kontraktu. Možné hodnoty: **Odeslání**, **potvrzené**, **selhání**.                         |
| AdditionalInformation | Další informace najdete na základě stavu |

Příklad odeslané **vytvoření kontraktu akce** neodpověděla Blockchain Workbench:

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

Příklad potvrzené **vytvoření kontraktu akce** neodpověděla Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

Pokud požadavek nebyl úspěšný, podrobnosti o chybě jsou zahrnuty v další informace.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Vstupní rozhraní API kódy chyb a zprávy

**Kód chyby: 4000: Chyba chybná žádost**
- Neplatné ID připojení
- CreateUserRequest deserializace se nezdařila
- CreateContractRequest deserializace se nezdařila
- CreateContractActionRequest deserializace se nezdařila
- Aplikace {identifikovat podle názvu aplikace} neexistuje.
- Aplikace {identifikovat podle názvu aplikace} nemá pracovního postupu
- UserChainIdentifier neexistuje.
- Kontrakt {označeny identifikátorem účetní knihy} neexistuje.
- Kontrakt {označeny identifikátorem účetní knihy} nemá žádné funkce {název funkce pracovního postupu}
- UserChainIdentifier neexistuje.

**Kód chyby: 4090: Chyba konfliktu**
- Uživatel už existuje.
- Smlouva již existuje.
- Kontrakt akce již existuje.

**Kód chyby: 5000: Vnitřní chyba serveru**
- Zprávy o výjimkách

## <a name="event-notifications"></a>Oznámení událostí

Oznamování událostí je možné upozornit uživatele a událostí, ke kterým dochází v Blockchain Workbench a, který je připojený k síti blockchain podřízených systémů. Oznamování událostí můžete využívat přímo v kódu nebo použít pomocí nástrojů jako Logic Apps a Flow k aktivaci toku dat do podřízených systémů.

V tématu [odkaz zprávu oznámení](#notification-message-reference) podrobnosti o různých zpráv, které může být přijata.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Spotřebovávajících událostí služby Event Grid s využitím Azure Functions

Pokud chce uživatel pomocí služby Event Grid oznámení o události, ke kterým dochází v Blockchain Workbench, můžete zpracovávat události ze služby Event Grid s využitím Azure Functions.

1. Vytvoření **aplikaci Azure Function App** na webu Azure Portal.
2. Vytvoření nové funkce.
3. Vyhledejte šablonu, kterou pro Event Grid. Kód základní šablony pro čtení zprávy se zobrazí. Podle potřeby upravte kód.
4. Uložte funkce. 
5. Vyberte službu Event Grid ze skupiny prostředků Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Spotřebovávajících událostí služby Event Grid pomocí funkce Logic Apps

1. Vytvořte nový **aplikace logiky Azure** na webu Azure Portal.
2. Při otevření aplikace logiky Azure na portálu, se výzva k výběru aktivační události. Vyberte **Azure Event Grid – při výskytu události prostředku**.
3. Po zobrazení návrháře postupu provádění se výzva k přihlášení.
4. Vyberte předplatné. Prostředek jako **Microsoft.EventGrid.Topics**. Vyberte **název prostředku** z názvu zdroje ze skupiny prostředků Azure Blockchain Workbench.
5. Vyberte službu Event Grid ze skupiny prostředků Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Používání témat Service Bus pro oznámení

Témata služby Service Bus je možné informovat uživatele o události, ke kterým dochází v Blockchain Workbench. 

1. Přejděte na Service Bus v rámci skupiny prostředků v aplikaci Workbench.
2. Vyberte **témata**.
3. Vyberte **odchozího přenosu dat tématu**.
4. Vytvořte nový odběr tohoto tématu. Získáte klíče pro něj.
5. Vytvořte program, který se přihlásí k odběru událostí z tohoto předplatného.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Přijímat zprávy služby Service Bus s Logic Apps

1. Vytvořte nový **aplikace logiky Azure** na webu Azure Portal.
2. Při otevření aplikace logiky Azure na portálu, se výzva k výběru aktivační události. Typ **služby Service Bus** do vyhledávacího pole a vyberte trigger vhodné pro typ interakce chcete mít službou Service Bus. Například **služby Service Bus – při doručení zprávy do odběru tématu (automatické dokončení)**.
3. Když se objeví návrháře postupu provádění, zadejte informace o připojení pro služby Service Bus.
4. Vyberte své předplatné a zadejte téma **externí aplikace workbench**.
5. Vyvíjejte logiku pro vaše aplikace, která využívá zprávy z této aktivační události.

## <a name="notification-message-reference"></a>Odkaz na zprávu oznámení

V závislosti na tom **OperationName**, zprávy s oznámením některou z následujících typů zpráv.

### <a name="block-message"></a>Zpráva o blokování.

Obsahuje informace o jednotlivých bloků. *BlockMessage* obsahuje části s informace na úrovni bloku a část s informací o transakcích.

| Název | Popis |
|------|-------------|
| blokovat | Obsahuje [blokovat informace](#block-information) |
| transakcí | Obsahuje kolekci [informací o transakcích](#transaction-information) pro blok |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Zasílání zpráv verze schématu |
| messageName | **BlockMessage** |
| AdditionalInformation | Další informace, které jsou k dispozici |

#### <a name="block-information"></a>Informace o bloku

| Název              | Popis |
|-------------------|-------------|
| blockId           | Jedinečný identifikátor pro blok uvnitř Azure Blockchain Workbench |
| BlockNumber       | Jedinečný identifikátor pro blok na hlavní knihy |
| blockHash         | Hodnota hash bloku |
| previousBlockHash | Hodnota hash předchozího bloku |
| blockTimestamp    | Časové razítko bloku |

#### <a name="transaction-information"></a>Informace o transakci

| Název               | Popis |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor pro transakce v rámci Azure Blockchain Workbench |
| TransactionHash    | Hodnota hash transakce na hlavní knihy |
| od               | Jedinečný identifikátor na účetní knihy pro původ transakce |
| na                 | Jedinečný identifikátor na účetní knihy pro cíl transakce |
| provisioningStatus | Určuje aktuální stav procesu zřízení pro transakce. Možné hodnoty: </br>0 – transakce byla vytvořena pomocí rozhraní API v databázi</br>1 – transakce byl odeslán na účetní knize</br>2 – transakce byla úspěšně do hlavní knihy</br>3 nebo 4 – transakce se nepodařilo potvrdit do hlavní knihy</br>5 - transakce se úspěšně potvrdilo do hlavní knihy |

Příklad *BlockMessage* z Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
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

### <a name="contract-message"></a>Kontrakt zprávy

Obsahuje informace o smlouvě o. Zpráva obsahuje oddíl s vlastnostmi smlouvy a část s informací o transakcích. V části transakce jsou zahrnuté všechny transakce, které byly upraveny smlouvy pro konkrétní blok.

| Název | Popis |
|------|-------------|
| blockId | Jedinečný identifikátor pro blok uvnitř Azure Blockchain Workbench |
| blockHash | Hodnota hash bloku |
| modifyingTransactions | [Transakcí, které upravit](#modifying-transaction-information) kontraktu |
| contractId | Jedinečný identifikátor pro kontrakt uvnitř Azure Blockchain Workbench |
| contractLedgerIdentifier | Jedinečný identifikátor pro kontrakt na hlavní knihy |
| contractProperties | [Vlastnosti smlouvy](#contract-properties) |
| isNewContract | Určuje, zda byl tento kontrakt nově vytvořen. Možné hodnoty jsou: true: Tato smlouva se vytvoří nové smlouvy. FALSE: této smlouvy je aktualizace smlouvy. |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Zasílání zpráv verze schématu |
| messageName | **ContractMessage** |
| AdditionalInformation | Další informace, které jsou k dispozici |

#### <a name="modifying-transaction-information"></a>Úprava informací o transakcích

| Název               | Popis |
|--------------------|-------------|
| transactionId | Jedinečný identifikátor pro transakce v rámci Azure Blockchain Workbench |
| TransactionHash | Hodnota hash transakce na hlavní knihy |
| od | Jedinečný identifikátor na účetní knihy pro původ transakce |
| na | Jedinečný identifikátor na účetní knihy pro cíl transakce |

#### <a name="contract-properties"></a>Vlastnosti smlouvy

| Název               | Popis |
|--------------------|-------------|
| workflowPropertyId | Jedinečný identifikátor pro vlastnosti pracovního postupu uvnitř Azure Blockchain Workbench |
| jméno | Název vlastnosti pracovního postupu |
| hodnota | Hodnota vlastnosti pracovního postupu |

Příklad *ContractMessage* z Blockchain Workbench:

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

### <a name="event-message-contract-function-invocation"></a>Zpráva o události: Volání funkce kontraktu

Obsahuje informace, když je vyvolána funkce smlouvy, jako je název funkce, vstupní parametry a volající funkce.

| Název | Popis |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| volající                      | [Informace o volajícím](#caller-information) |
| contractId                  | Jedinečný identifikátor pro kontrakt uvnitř Azure Blockchain Workbench |
| contractLedgerIdentifier    | Jedinečný identifikátor pro kontrakt na hlavní knihy |
| functionName                | Název funkce |
| parameters                  | [Informace o parametrech](#parameter-information) |
| Transakce                 | [Informace o transakci](#eventmessage-transaction-information) |
| inTransactionSequenceNumber | Pořadové číslo sady transakcí v bloku |
| connectionId                | Jedinečný identifikátor pro připojení |
| messageSchemaVersion        | Zasílání zpráv verze schématu |
| messageName                 | **EventMessage** |
| AdditionalInformation       | Další informace, které jsou k dispozici |

#### <a name="caller-information"></a>Informace o volajícím

| Název | Popis |
|------|-------------|
| type | Zadejte volajícího, jako je uživatel nebo kontrakt |
| id | Jedinečný identifikátor volajícího uvnitř Azure Blockchain Workbench |
| ledgerIdentifier | Jedinečný identifikátor volajícího na hlavní knihy |

#### <a name="parameter-information"></a>Informace o parametrech

| Název | Popis |
|------|-------------|
| jméno | Název parametru |
| hodnota | Hodnota parametru |

#### <a name="event-message-transaction-information"></a>Informace o transakcích zprávy událostí

| Název               | Popis |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor pro transakce v rámci Azure Blockchain Workbench |
| TransactionHash    | Hodnota hash transakce na hlavní knihy |
| od               | Jedinečný identifikátor na účetní knihy pro původ transakce |
| na                 | Jedinečný identifikátor na účetní knihy pro cíl transakce |

Příklad *zpráva události ContractFunctionInvocation* z Blockchain Workbench:

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

### <a name="event-message-application-ingestion"></a>Zpráva o události: Příjem aplikace

Obsahuje informace při nahrání aplikace do aplikace Workbench, jako například název a verze aplikace nahraje.

| Název | Popis |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Jedinečný identifikátor pro aplikaci v Azure Blockchain Workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationDefinitionLocation | Adresa URL, kde se nachází konfigurační soubor aplikace |
| contractCodes | Kolekce [smlouva kódech](#contract-code-information) pro aplikaci |
| ApplicationRoles | Kolekce [aplikační role](#application-role-information) pro aplikaci |
| applicationWorkflows | Kolekce [pracovní postupy aplikace](#application-workflow-information) pro aplikaci |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Zasílání zpráv verze schématu |
| messageName | **EventMessage** |
| AdditionalInformation | Další informace, které jsou k dispozici zde zahrnuje stavy pracovního postupu aplikace a informace o přechodu. |

#### <a name="contract-code-information"></a>Informace o kódu kontraktu

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor souboru kontraktu kódu uvnitř Azure Blockchain Workbench |
| ledgerId | Jedinečný identifikátor pro knihy uvnitř Azure Blockchain Workbench |
| location | Adresa URL, kde je umístěn soubor kódu kontraktu |

#### <a name="application-role-information"></a>Informace o rolích aplikace

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro aplikační role v Azure Blockchain Workbench |
| jméno | Název role aplikace |

#### <a name="application-workflow-information"></a>Informace o aplikaci pracovního postupu

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro pracovní postupy aplikace v Azure Blockchain Workbench |
| jméno | Název pracovního postupu aplikace |
| displayName | Zobrazovaný název aplikace pracovního postupu |
| functions | Kolekce [funkce pro pracovní postupy aplikace ](#workflow-function-information)|
| stavy | Kolekce [stavy pracovního postupu aplikace](#workflow-state-information) |
| properties | Aplikace [informace o vlastnostech pracovního postupu](#workflow-property-information) |

##### <a name="workflow-function-information"></a>Informace o pracovním postupu – funkce

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro funkce aplikace pracovního postupu uvnitř Azure Blockchain Workbench |
| jméno | Název funkce |
| parameters | Parametry pro tuto funkci |

##### <a name="workflow-state-information"></a>Informace o stavu pracovního postupu

| Název | Popis |
|------|-------------|
| jméno | Název státu |
| displayName | Název stavu zobrazení |
| Styl | Stav stylu (úspěch nebo neúspěch) |

##### <a name="workflow-property-information"></a>Informace o vlastnosti pracovního postupu

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro vlastnosti pracovního postupu aplikace v Azure Blockchain Workbench |
| jméno | Název vlastnosti |
| type | Typ vlastnosti |

Příklad *zpráva události ApplicationIngestion* z Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
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
    ]
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
                            "DisplayName": "Accept"
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

### <a name="event-message-role-assignment"></a>Zpráva o události: Přiřazení role

Obsahuje informace, když uživatel má přiřazenou roli v aplikaci Workbench, například kdo vykonal přiřazení role a název role a příslušné aplikace.

| Název | Popis |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Jedinečný identifikátor pro aplikaci v Azure Blockchain Workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationRole        | Informace o [aplikační role](#roleassignment-application-role) |
| Pověřující osoby               | Informace o [Pověřující osoby](#roleassignment-assigner) |
| pověřené osoby               | Informace o [pověřené osoby](#roleassignment-assignee) |
| connectionId           | Jedinečný identifikátor pro připojení |
| messageSchemaVersion   | Zasílání zpráv verze schématu |
| messageName            | **EventMessage** |
| AdditionalInformation  | Další informace, které jsou k dispozici |

#### <a name="roleassignment-application-role"></a>RoleAssignment aplikační role

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro aplikační role v Azure Blockchain Workbench |
| jméno | Název role aplikace |

#### <a name="roleassignment-assigner"></a>Přidělovač RoleAssignment

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor uživatele v Azure Blockchain Workbench |
| type | Typ Pověřující osoby |
| ChainIdentifier | Jedinečný identifikátor uživatele v hlavní knihy |

#### <a name="roleassignment-assignee"></a>RoleAssignment pověřené osoby

| Název | Popis |
|------|-------------|
| id | Jedinečný identifikátor uživatele v Azure Blockchain Workbench |
| type | Typ pověřené osoby |
| ChainIdentifier | Jedinečný identifikátor uživatele v hlavní knihy |

Příklad *zpráva události RoleAssignment* z Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
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

## <a name="next-steps"></a>Další postup

- [Vzory integrace inteligentní kontraktu](integration-patterns.md)