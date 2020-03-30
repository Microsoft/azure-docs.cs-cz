---
title: Integrace se s Azure Blockchain Workbench pomocí zpráv
description: Přehled používání zpráv k integraci Azure Blockchain Workbench Preview s jinými systémy.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 14bd0f84bc9490d95d3dbe0b9f122882f0d2059d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324509"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrace zasílání zpráv Azure Blockchain Workbench

Kromě poskytování rozhraní REST API poskytuje Azure Blockchain Workbench také integraci založenou na zasílání zpráv. Workbench publikuje události zaměřené na hlavní knihu prostřednictvím Služby Azure Event Grid, což umožňuje následným zákazníkům přijímat data nebo provést akci na základě těchto událostí. Pro klienty, kteří vyžadují spolehlivé zasílání zpráv, Azure Blockchain Workbench doručuje zprávy do koncového bodu Azure Service Bus také.

## <a name="input-apis"></a>Vstupní api

Pokud chcete zahájit transakce z externích systémů k vytváření uživatelů, vytváření smluv a aktualizaci smluv, můžete k provádění transakcí v hlavní knize použít vstupní api zasílání zpráv. Viz [ukázky integrace zasílání zpráv](https://aka.ms/blockchain-workbench-integration-sample) pro ukázku, která ukazuje vstupní api.

Následují aktuálně dostupná vstupní rozhraní API.

### <a name="create-user"></a>Vytvořit uživatele

Vytvoří nového uživatele.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                      |
|----------------------|------------------------------------------------------|
| Requestid            | Identifikátor GUID dodaný klientem                                |
| firstName            | Křestní jméno uživatele                              |
| lastName             | Příjmení uživatele                               |
| Emailaddress         | E-mailová adresa uživatele                           |
| externalId           | ID objektu Azure AD uživatele                      |
| connectionId         | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion | Verze schématu zasílání zpráv                            |
| messageName          | **Vytvořitpožadavek uživatele**                               |

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

Blockchain Workbench vrací odpověď s následujícími poli:

| **Název**              | **Popis**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| Requestid             | Identifikátor GUID dodaný klientem |
| userId                | ID uživatele, který byl vytvořen |
| userChainIdentifier   | Adresa uživatele, který byl vytvořen v blockchainové síti. V ethereum je adresa uživatele **v řetězci.** |
| connectionId          | Jedinečný identifikátor pro připojení blockchain|
| messageSchemaVersion  | Verze schématu zasílání zpráv |
| messageName           | **CreateUserUpdate** |
| status                | Stav požadavku na vytvoření uživatele.  Pokud je úspěšná, hodnota je **Úspěch**. Při selhání je hodnota **Selhání**.     |
| dalšíinformace | Další informace poskytnuté na základě stavu |

Příklad **úspěšného vytvoření** odpovědi uživatele z Blockchain Workbench:

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

Pokud byla žádost neúspěšná, podrobnosti o selhání jsou zahrnuty v další informace.

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

### <a name="create-contract"></a>Vytvořit smlouvu

Vytvoří novou smlouvu.

Požadavek vyžaduje následující pole:

| **Název**             | **Popis**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| Requestid            | Identifikátor GUID dodaný klientem |
| userChainIdentifier  | Adresa uživatele, který byl vytvořen v blockchainové síti. V ethereum je tato adresa adresa uživatele **na řetězové** adrese. |
| applicationName      | Název žádosti |
| version              | Verze aplikace Povinné, pokud máte povoleno více verzí aplikace. V opačném případě je verze volitelná. Další informace o správě verzí aplikací najdete v [tématu Správa verzí aplikací Azure Blockchain Workbench](version-app.md). |
| workflowName         | Název pracovního postupu |
| parameters           | Parametry vstup pro vytvoření smlouvy |
| connectionId         | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| messageName          | **Vytvořit požadavek na smlouvu** |

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

Blockchain Workbench vrací odpověď s následujícími poli:

| **Název**                 | **Popis**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| Requestid                | Identifikátor GUID dodaný klientem                                                             |
| contractId               | Jedinečný identifikátor smlouvy v azure blockchainworkbench |
| contractLedgerIdentifier | Adresa smlouvy v hlavní knize                                            |
| connectionId             | Jedinečný identifikátor pro připojení blockchain                               |
| messageSchemaVersion     | Verze schématu zasílání zpráv                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Stav žádosti o vytvoření smlouvy.  Možné hodnoty: **Odesláno**, **Potvrzeno**, **Selhání**.  |
| dalšíinformace    | Další informace poskytnuté na základě stavu                              |

Příklad odeslané **odpovědi vytvořit smlouvu** z Blockchain Workbench:

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

Příklad potvrzené odpovědi **na vytvoření smlouvy** z Blockchain Workbench:

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

Pokud byla žádost neúspěšná, podrobnosti o selhání jsou zahrnuty v další informace.

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

### <a name="create-contract-action"></a>Vytvořit akci smlouvy

Vytvoří novou akci smlouvy.

Požadavek vyžaduje následující pole:

| **Název**                 | **Popis**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Requestid                | Identifikátor GUID dodaný klientem |
| userChainIdentifier      | Adresa uživatele, který byl vytvořen v blockchainové síti. V ethereum je tato adresa adresa uživatele **na řetězové** adrese. |
| contractLedgerIdentifier | Adresa smlouvy v hlavní knize |
| version                  | Verze aplikace Povinné, pokud máte povoleno více verzí aplikace. V opačném případě je verze volitelná. Další informace o správě verzí aplikací najdete v [tématu Správa verzí aplikací Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Název funkce pracovního postupu |
| parameters               | Parametry vstup pro vytvoření smlouvy |
| connectionId             | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion     | Verze schématu zasílání zpráv |
| messageName              | **Vytvořit požadavek na akci** |

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

Blockchain Workbench vrací odpověď s následujícími poli:

| **Název**              | **Popis**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| Requestid             | Identifikátor GUID dodaný klientem|
| contractId            | Jedinečný identifikátor smlouvy v azure blockchainworkbench |
| connectionId          | Jedinečný identifikátor pro připojení blockchain |
| messageSchemaVersion  | Verze schématu zasílání zpráv |
| messageName           | **Vytvořit aktualizaci akcenění kontraktů** |
| status                | Stav žádosti o akci smlouvy. Možné hodnoty: **Odesláno**, **Potvrzeno**, **Selhání**.                         |
| dalšíinformace | Další informace poskytnuté na základě stavu |

Příklad odeslané odpovědi **na akci smlouvy** z Blockchain Workbench:

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

Příklad potvrzené odpovědi na **akci vytvoření smlouvy** z Blockchain Workbench:

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

Pokud byla žádost neúspěšná, podrobnosti o selhání jsou zahrnuty v další informace.

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

### <a name="input-api-error-codes-and-messages"></a>Vstupní kódy chyb rozhraní API a zprávy

**Kód chyby 4000: Chyba chybného požadavku**
- Neplatné id připojení
- Deserializace createUserRequest se nezdařila.
- Deserializace požadavku CreateContractRequest se nezdařila.
- Deserializace požadavku CreateContractActionRequest se nezdařila.
- Aplikace {identifikovaná názvem aplikace} neexistuje.
- Aplikace {identified by application name} does not have workflow Application {identified by application name} does not have workflow
- UserChainIdentifier neexistuje.
- Smlouva {identifikovaná identifikátorem hlavní knihy} neexistuje.
- Smlouva {identifikovaná identifikátorem hlavní knihy} nemá funkci {název funkce pracovního postupu}
- UserChainIdentifier neexistuje.

**Kód chyby 4090: Chyba konfliktu**
- Uživatel již existuje.
- Smlouva již existuje.
- Akce smlouvy již existuje.

**Kód chyby 5000: Vnitřní chyba serveru**
- Zprávy o výjimce

## <a name="event-notifications"></a>Oznámení událostí

Oznámení o událostech lze použít k upozornění uživatelů a navazujících systémů na události, které se dějí v Blockchain Workbench a blockchainové síti, ke které je připojen. Oznámení událostí lze spotřebovat přímo v kódu nebo použít s nástroji, jako jsou logic apps a tok pro aktivaci toku dat do podřízené systémy.

Podrobnosti o různých zprávách, které lze přijímat, naleznete [v tématu Odkaz na oznámení.](#notification-message-reference)

### <a name="consuming-event-grid-events-with-azure-functions"></a>Náročné události gridu událostí pomocí funkcí Azure

Pokud uživatel chce použít Event Grid k upozornění na události, ke kterým dochází v Blockchain Workbench, můžete spotřebovat události z Event Grid pomocí Funkce Azure.

1. Vytvořte **aplikaci Azure Function Na** webu Azure Portal.
2. Vytvořte novou funkci.
3. Vyhledejte šablonu pro mřížku událostí. Zobrazí se základní kód šablony pro čtení zprávy. Podle potřeby upravte kód.
4. Uložte funkci. 
5. Vyberte mřížku událostí ze skupiny prostředků Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Náročné události gridu událostí pomocí aplikací logiky

1. Vytvořte novou **aplikaci Azure Logic na** webu Azure Portal.
2. Při otevření aplikace Azure Logic Na portálu budete vyzváni k výběru aktivační události. Vyberte **Azure Event Grid – když dojde k události prostředku**.
3. Po zobrazení návrháře pracovního postupu budete vyzváni k přihlášení.
4. Vyberte předplatné. Zdroj jako **Microsoft.EventGrid.Topics**. Vyberte **název prostředku** z názvu prostředku ze skupiny prostředků Azure Blockchain Workbench.
5. Vyberte mřížku událostí ze skupiny prostředků Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Použití témat služby Service Bus pro oznámení

Témata služby Service Bus lze uživatele upozornit na události, ke kterým dochází v blockchainworkbench. 

1. Přejděte na service bus ve skupině prostředků workbench.
2. Vyberte **možnost Témata**.
3. Vyberte **téma výstupu**.
4. Vytvořte nové předplatné tohoto tématu. Získejte klíč pro něj.
5. Vytvořte program, který se přihlásí k odběru událostí z tohoto předplatného.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Náročné zprávy služby Service Bus s aplikacemi logiky

1. Vytvořte novou **aplikaci Azure Logic na** webu Azure Portal.
2. Při otevření aplikace Azure Logic Na portálu budete vyzváni k výběru aktivační události. Do vyhledávacího pole zadejte **službu Service Bus** a vyberte aktivační událost vhodnou pro typ interakce, kterou chcete mít se službou Service Bus. Například **Service Bus – Při přijetí zprávy v tematickém předplatném (automatické dokončování)**.
3. Při zobrazení návrháře pracovního postupu zadejte informace o připojení pro sběrnici Service Bus.
4. Vyberte předplatné a zadejte téma **workbench-external**.
5. Vytvořte logiku pro vaši aplikaci, která využívá zprávu z této aktivační události.

## <a name="notification-message-reference"></a>Odkaz na oznámení

V závislosti na **messageName**, oznámení mají jeden z následujících typů zpráv.

### <a name="block-message"></a>Blokovat zprávu

Obsahuje informace o jednotlivých blocích. *BlockMessage* obsahuje oddíl s informacemi o úrovni bloku a oddíl s informacemi o transakcích.

| Name (Název) | Popis |
|------|-------------|
| blokovat | Obsahuje [informace o bloku](#block-information) |
| transactions | Obsahuje [informace o transakci](#transaction-information) kolekce pro blok |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| messageName | **BlockMessage** |
| dalšíinformace | Další poskytnuté informace |

#### <a name="block-information"></a>Informace o blokování

| Name (Název)              | Popis |
|-------------------|-------------|
| blockId           | Jedinečný identifikátor bloku uvnitř pracovní plochy Azure Blockchain Workbench |
| blockČíslo       | Jedinečný identifikátor bloku v hlavní knize |
| blokHash         | Hash bloku |
| předchozíBlockHash | Hash předchozího bloku |
| blockTimestamp    | Časové razítko bloku |

#### <a name="transaction-information"></a>Informace o transakci

| Name (Název)               | Popis |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor pro transakci uvnitř Azure Blockchain Workbench |
| transakceHash    | Hodnota hash transakce v hlavní knize |
| Z               | Jedinečný identifikátor v hlavní knize pro původ transakce |
| na                 | Jedinečný identifikátor v hlavní knize pro cíl transakce |
| zřizováníStav | Identifikuje aktuální stav procesu zřizování pro transakci. Možné hodnoty: </br>0 – Transakce byla vytvořena rozhraním API v databázi.</br>1 – Transakce byla odeslána do hlavní knihy.</br>2 – Transakce byla úspěšně potvrzena do hlavní knihy.</br>3 nebo 4 - Transakce se nepodařilo zavázat do hlavní knihy</br>5 - Transakce byla úspěšně potvrzena do hlavní knihy. |

Příklad *BlockMessage* z Blockchain Workbench:

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

Obsahuje informace o smlouvě. Zpráva obsahuje oddíl s vlastnostmi smlouvy a oddíl s informacemi o transakcích. Všechny transakce, které změnily smlouvu pro konkrétní blok, jsou zahrnuty v části transakce.

| Name (Název) | Popis |
|------|-------------|
| blockId | Jedinečný identifikátor bloku uvnitř pracovní plochy Azure Blockchain Workbench |
| blokHash | Hash bloku |
| úpravatransakcí | [Transakce, které změnily](#modifying-transaction-information) smlouvu |
| contractId | Jedinečný identifikátor smlouvy v azure blockchainworkbench |
| contractLedgerIdentifier | Jedinečný identifikátor smlouvy v hlavní knize |
| contractProperties | [Vlastnosti smlouvy](#contract-properties) |
| isNewContract | Označuje, zda byla tato smlouva nově vytvořena. Možné hodnoty jsou: true: tato smlouva byla vytvořena nová smlouva. false: tato smlouva je aktualizace smlouvy. |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| messageName | **Zpráva o smlouvě** |
| dalšíinformace | Další poskytnuté informace |

#### <a name="modifying-transaction-information"></a>Úprava informací o transakcích

| Name (Název)               | Popis |
|--------------------|-------------|
| transactionId | Jedinečný identifikátor pro transakci uvnitř Azure Blockchain Workbench |
| transakceHash | Hodnota hash transakce v hlavní knize |
| Z | Jedinečný identifikátor v hlavní knize pro původ transakce |
| na | Jedinečný identifikátor v hlavní knize pro cíl transakce |

#### <a name="contract-properties"></a>Vlastnosti smlouvy

| Name (Název)               | Popis |
|--------------------|-------------|
| workflow workflowPropertyId | Jedinečný identifikátor pro vlastnost pracovního postupu v pracovní plochy Azure Blockchain Workbench |
| jméno | Název vlastnosti pracovního postupu |
| value | Hodnota vlastnosti pracovního postupu |

Příklad *zprávy contractmessage* z Blockchain Workbench:

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

### <a name="event-message-contract-function-invocation"></a>Zpráva o události: Vyvolání funkce smlouvy

Obsahuje informace při vyvolání funkce smlouvy, jako je například název funkce, vstup parametrů a volající funkce.

| Name (Název) | Popis |
|------|-------------|
| Eventname                   | **ContractFunctionInvocation** |
| Volající                      | [Informace o volajícím](#caller-information) |
| contractId                  | Jedinečný identifikátor smlouvy v azure blockchainworkbench |
| contractLedgerIdentifier    | Jedinečný identifikátor smlouvy v hlavní knize |
| název funkce                | Název funkce |
| parameters                  | [Informace o parametrech](#parameter-information) |
| Transakce                 | Informace o transakci |
| inTransactionSequenceNumber | Pořadové číslo transakce v bloku |
| connectionId                | Jedinečný identifikátor pro připojení |
| messageSchemaVersion        | Verze schématu zasílání zpráv |
| messageName                 | **Zpráva o události** |
| dalšíinformace       | Další poskytnuté informace |

#### <a name="caller-information"></a>Informace o volajícím

| Name (Název) | Popis |
|------|-------------|
| type | Typ volajícího, například uživatele nebo smlouvy |
| id | Jedinečný identifikátor volajícího v pracovní matné službě Azure Blockchain Workbench |
| hlavní knihaIdentifikátor | Jedinečný identifikátor volajícího v hlavní knize |

#### <a name="parameter-information"></a>Informace o parametrech

| Name (Název) | Popis |
|------|-------------|
| jméno | Název parametru |
| value | Hodnota parametru |

#### <a name="event-message-transaction-information"></a>Informace o transakci zprávy o události

| Name (Název)               | Popis |
|--------------------|-------------|
| transactionId      | Jedinečný identifikátor pro transakci uvnitř Azure Blockchain Workbench |
| transakceHash    | Hodnota hash transakce v hlavní knize |
| Z               | Jedinečný identifikátor v hlavní knize pro původ transakce |
| na                 | Jedinečný identifikátor v hlavní knize pro cíl transakce |

Příklad *eventmessage contractfunctioninvocation* z Blockchain Workbench:

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

### <a name="event-message-application-ingestion"></a>Zpráva o události: Přihlašování aplikace

Obsahuje informace při odeslání aplikace do Workbench, jako je například název a verze nahrané aplikace.

| Name (Název) | Popis |
|------|-------------|
| Eventname | **ApplicationIngestion** |
| applicationId | Jedinečný identifikátor aplikace uvnitř azure blockchain workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationDefinitionLocation | Adresa URL, ve které je umístěn konfigurační soubor aplikace |
| contractCodes | Výběr [kódů smluv](#contract-code-information) pro aplikaci |
| aplikaceRole | Kolekce [rolí aplikací](#application-role-information) pro aplikaci |
| aplikační pracovní postupy | Kolekce [pracovních postupů aplikace](#application-workflow-information) pro aplikaci |
| connectionId | Jedinečný identifikátor pro připojení |
| messageSchemaVersion | Verze schématu zasílání zpráv |
| messageName | **Zpráva o události** |
| dalšíinformace | Další informace zde uvedené zahrnují stavy pracovního postupu aplikace a informace o přechodu. |

#### <a name="contract-code-information"></a>Informace o kódu smlouvy

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor souboru kódu smlouvy uvnitř Azure Blockchain Workbench |
| ledgerId | Jedinečný identifikátor pro hlavní knihu v pracovní mitové matné službě Azure Blockchain Workbench |
| location | Adresa URL, ve které je soubor kódu smlouvy umístěn |

#### <a name="application-role-information"></a>Informace o roli aplikace

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor role aplikace v pracovní matné službě Azure Blockchain Workbench |
| jméno | Název role aplikace |

#### <a name="application-workflow-information"></a>Informace o pracovním postupu aplikace

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor pracovního postupu aplikace v pracovní matné službě Azure Blockchain Workbench |
| jméno | Název pracovního postupu aplikace |
| displayName | Zobrazovaný název pracovního postupu aplikace |
|  – funkce | Kolekce [funkcí pro pracovní postup aplikace](#workflow-function-information)|
| Státy | Kolekce [stavů pro pracovní postup aplikace](#workflow-state-information) |
| properties | [Informace o vlastnostech pracovního postupu](#workflow-property-information) aplikace |

##### <a name="workflow-function-information"></a>Informace o funkci pracovního postupu

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor funkce pracovního postupu aplikace v pracovní matné pracovní plochy Azure Blockchain |
| jméno | Název funkce |
| parameters | Parametry pro funkci |

##### <a name="workflow-state-information"></a>Informace o stavu pracovního postupu

| Name (Název) | Popis |
|------|-------------|
| jméno | Název státu |
| displayName | Zobrazovaný název stavu |
|  – styl | Styl stavu (úspěch nebo neúspěch) |

##### <a name="workflow-property-information"></a>Informace o vlastnostech pracovního postupu

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor pro vlastnost pracovního postupu aplikace v pracovní matné pracovní plochy Azure Blockchain |
| jméno | Název vlastnosti |
| type | Typ vlastnosti |

Příklad *aplikace EventMessage Ingestion* z Blockchain Workbench:

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

### <a name="event-message-role-assignment"></a>Zpráva o události: Přiřazení role

Obsahuje informace, když je uživateli přiřazena role v Workbench, například kdo provedl přiřazení role a název role a odpovídající aplikace.

| Name (Název) | Popis |
|------|-------------|
| Eventname | **Přiřazení role** |
| applicationId | Jedinečný identifikátor aplikace uvnitř azure blockchain workbench |
| applicationName | Název aplikace |
| applicationDisplayName | Zobrazovaný název aplikace |
| applicationVersion | Verze aplikace |
| applicationRole        | Informace o [roli aplikace](#roleassignment-application-role) |
| přiřaditel               | Informace o [postupovacím příkazci](#roleassignment-assigner) |
| postupník               | Informace o [postupníkovi](#roleassignment-assignee) |
| connectionId           | Jedinečný identifikátor pro připojení |
| messageSchemaVersion   | Verze schématu zasílání zpráv |
| messageName            | **Zpráva o události** |
| dalšíinformace  | Další poskytnuté informace |

#### <a name="roleassignment-application-role"></a>Role aplikace RoleAssignment

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor role aplikace v pracovní matné službě Azure Blockchain Workbench |
| jméno | Název role aplikace |

#### <a name="roleassignment-assigner"></a>Přiřazení role

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor uživatele v pracovní plochy Azure Blockchain Workbench |
| type | Typ přiřazení |
| chainIdentifier | Jedinečný identifikátor uživatele v hlavní knize |

#### <a name="roleassignment-assignee"></a>Přiřazení role postupník

| Name (Název) | Popis |
|------|-------------|
| id | Jedinečný identifikátor uživatele v pracovní plochy Azure Blockchain Workbench |
| type | Typ postupníka |
| chainIdentifier | Jedinečný identifikátor uživatele v hlavní knize |

Příklad *role Assignment EventMessage* z blockchainworkbench:

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
