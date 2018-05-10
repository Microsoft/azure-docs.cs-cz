---
title: Přehled Azure Blockchain Workbench zprávy
description: Přehled používání zprávy v Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 9fa0d74b2c07d3e460abc54ea6ef9733f07a64d1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-blockchain-workbench-messages-overview"></a>Přehled Azure Blockchain Workbench zprávy

Kromě rozhraní REST API, Azure Blockchain Workbench také nabízí integraci na základě zasílání zpráv. Workbench publikuje zaměřené na hlavní knihy událostí prostřednictvím Azure událostí mřížky, povolení podřízené příjemcům ingestovat data nebo provést akci na základě těchto událostí. Pro ty klienty, které vyžadují spolehlivé zasílání zpráv Azure Blockchain Workbench přináší zprávy koncový Azure Service Bus.

Vývojáři mají také vyjádřit zájem o schopnost komunikovat inicializace transakce k vytváření uživatelů, vytvoření smluv a aktualizaci kontrakty na hlavní knihy externími systémy. Když tato funkce není aktuálně vystavený ve verzi public preview, vzorku, který poskytuje tuto možnost najdete na [ http://aka.ms/blockchain-workbench-integration-sample ](http://aka.ms/blockchain-workbench-integration-sample).


## <a name="event-notifications"></a>Oznámení událostí

Oznamování událostí lze použít pro upozornění uživatelů a podřízené systémy události, které ve Workbench a, který je připojený k síti blockchain dojde. Oznamování událostí můžete použít přímo v kódu nebo pomocí nástrojů, například Logic Apps a toku používá k aktivaci tok dat pro příjem dat systémy.

V tématu [odkaz zprávu oznámení](#notification-message-reference) podrobnosti o různých zprávy, které můžou přijímat.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Využívání událostí mřížky události s Azure Functions

Pokud chce uživatel použít událost mřížky oznámení o událostech, které ve Blockchain Workbench dojde, může přijímat události z události mřížky pomocí Azure Functions.

1. Vytvoření **aplikaci funkce Azure** na portálu Azure.
2. Vytvořte novou funkci.
3. Vyhledejte šablonu, kterou pro událost mřížky. Kód základní šablony pro čtení zpráva se zobrazí. Podle potřeby změnit kód.
4. Uložte funkce. 
5. Vyberte události mřížky ze skupiny prostředků Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Využívání událost mřížky události s Logic Apps

1.  Vytvořte novou **aplikace logiky Azure** na portálu Azure.
2.  Při otevření aplikace logiky Azure na portálu, se zobrazí výzva k výběru aktivační událost. Vyberte **tabulka událostí Azure – Pokud se vyskytne událost prostředků**.
3. Když se zobrazí Návrháři pracovních postupů, se výzva k přihlášení.
4. Vyberte předplatné. Prostředek jako **Microsoft.EventGrid.Topics**. Vyberte **název prostředku** od názvu prostředku ze skupiny prostředků Azure Blockchain Workbench.
5. Vyberte události mřížky ze skupiny prostředků Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Pomocí témata služby Service Bus pro oznámení

Témata služby Service Bus slouží k upozornění uživatele o událostech, které ve Blockchain Workbench dojde. 

1.  Přejděte do služby Service Bus v rámci skupiny prostředků nástroje Workbench.
2.  Vyberte **témata**.
3.  Vyberte **workbench externí**.
4.  Vytvořte nový odběr tohoto tématu. Získejte klíč pro ni.
5.  Vytvořte program, který se přihlásí k událostem z tohoto předplatného.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Využívání zprávy služby Service Bus s Logic Apps

1. Vytvořte novou **aplikace logiky Azure** na portálu Azure.
2.  Při otevření aplikace logiky Azure na portálu, se zobrazí výzva k výběru aktivační událost. Typ **Service Bus** do vyhledávacího pole a vyberte vhodné pro typ interakce aktivační událost můžete chtít, aby se Service Bus. Například **Service Bus – při příjmu zprávy v odběru tématu (automatické dokončování)**.
3. Když se zobrazí Návrháři pracovních postupů, zadejte informace o připojení pro Service Bus.
4. Vyberte předplatné a zadejte téma **workbench externí**.
5. Vývoj logiku pro vaše aplikace, která využívá zprávy z této aktivační události.

## <a name="notification-message-reference"></a>Odkaz na zprávu oznámení

V závislosti na OperationName zprávy s oznámením mít jednu z následujících typů zpráv.

### <a name="accountcreated"></a>AccountCreated

Označuje, že nový účet má byla pro přidání do zadaného řetězce.

| Název    | Popis  |
|----------|--------------|
| ID uživatele  | ID uživatele, který byl vytvořen |
| ChainIdentifier | Adresa uživatele, který byl vytvořen v síti blockchain. V Ethereum bude adresa "pro řetězec" uživatele. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Označuje, že žádost o byl proveden vložit nebo aktualizovat kontraktu na distribuované hlavní knihy.

| Název | Popis |
|-----|--------------|
| ChainID | Jedinečný identifikátor pro řetězec přidružený k požadavku.|
  Blockid % | Jedinečný identifikátor pro bloku na hlavní knihy.|
  ContractId | Jedinečný identifikátor pro daný kontrakt.|
  ContractAddress |       Adresa kontrakt na hlavní knihy.|
  TransactionHash  |     Hodnota hash transakce na hlavní knihy.|
  OriginatingAddress |   Adresa původce transakce.|
  Název akce       |     Název akce.|
  IsUpdate        |      Označuje, jestli jde o aktualizaci.|
  Parametry       |     Seznam objektů, které identifikují název, hodnotu a datový typ parametry odeslané akce.|
  TopLevelInputParams |  Ve scénářích, kde je kontraktu připojené k jedné nebo více smluv jsou tyto parametry z nejvyšší úrovně kontrakt. |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

Označuje, že provedl se požadavek k provedení akce na konkrétní smlouvě na distribuované hlavní knihy.

| Název                     | Popis                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Jedinečný identifikátor pro tuto akci kontraktu                                                                                                                                |
| ChainIdentifier          | Jedinečný identifikátor pro řetězec                                                                                                                                           |
| ID připojení             | Jedinečný identifikátor pro připojení                                                                                                                                      |
| UserChainIdentifier      | Adresa uživatele, který byl vytvořen v síti blockchain. V Ethereum bude adresa "pro řetězec" uživatele.                                                     |
| ContractLedgerIdentifier | Adresa kontrakt na hlavní knihy.                                                                                                                                        |
| WorkflowFunctionName     | Název funkce pracovního postupu.                                                                                                                                                |
| WorkflowName             | Název pracovního postupu.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Adresa url kontraktu v úložišti objektů blob.                                                                                                                                      |
| ContractActionParameters | Parametry pro akce kontrakt.                                                                                                                                           |
| TransactionHash          | Hodnota hash transakce na hlavní knihy.                                                                                                                                    |
| Stav zřizování      | Aktuální stav zřizování akce.</br>0 – vytvoření</br>1 – v procesu</br>2 – dokončení</br> Dokončení označuje potvrzení z hlavní knihy, tomto byl úspěšně přidán.                                               |
|                          |                                                                                                                                                                               |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

Označuje, že byl proveden žádost o aktualizaci vyrovnávání uživatele na konkrétní distribuované hlavní knihy.

> [!NOTE]
> Tato zpráva je generována pouze pro tyto hlavní knihy, které vyžadují finančních prostředků účty.
> 

| Název    | Popis                              |
|---------|------------------------------------------|
| Adresa | Adresa uživatele, který byl financované. |
| Vyrovnávat | Vyrovnání vyrovnávání uživatele.         |
| ChainID | Jedinečný identifikátor pro řetězec.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Zpráva znamená, že byl proveden žádost o přidání bloku na distribuované hlavní knihy.

| Název           | Popis                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Jedinečný identifikátor řetězu, do které byl přidán bloku.             |
| Blockid %        | Jedinečný identifikátor pro blok uvnitř Azure Blockchain Workbench. |
| BlockHash      | Hodnota hash bloku.                                                 |
| BlockTimeStamp | Časové razítko bloku.                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

Zpráva obsahuje podrobné informace na žádost o přidání transakce na distribuované hlavní knihy.

| Název            | Popis                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Jedinečný identifikátor řetězu, do které byl přidán bloku.             |
| Blockid %         | Jedinečný identifikátor pro blok uvnitř Azure Blockchain Workbench. |
| TransactionHash | Hodnota hash transakce.                                           |
| Od            | Adresa původce transakce.                      |
| Akce              | Adresa zamýšlený příjemce transakce.              |
| Hodnota           | Hodnota součástí transakce.                                 |
| IsAppBuilderTx  | Označuje, jestli jde Blockchain Workbench transakce.                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

Poskytuje podrobné informace o přiřazení identifikátoru řetězce pro kontraktu. Například v Ethereum blockchain, adresu kontraktu na hlavní knihy.

| Název            | Popis                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Toto je jedinečný identifikátor pro kontrakt uvnitř Azure Blockchain Workbench. |
| ChainIdentifier | Toto je identifikátor pro kontrakt v řetězu.                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="next-steps"></a>Další postup

* [Azure Blockchain Workbench architektura](blockchain-workbench-architecture.md)