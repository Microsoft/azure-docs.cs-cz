---
title: Přehled integrace zpráv služby Azure Blockchain Workbench
description: Základní informace o použití zpráv v aplikaci Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b4a816c887d1cca78ff845858dce29049946b09f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235985"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integrace zasílání zpráv Azure Blockchain Workbench

Kromě rozhraní REST API, Azure Blockchain Workbench umožňuje také nabízí integrace založené na zasílání zpráv. Aplikace Workbench publikuje zaměřené na účetní knihy události prostřednictvím služby Azure Event Grid, která podřízené zákazníkům umožní ingestovat data nebo provést akce na základě těchto událostí. U těchto klientů, které vyžadují spolehlivé zasílání zpráv Azure Blockchain Workbench předává zprávy ostatním koncový Azure Service Bus.

Vývojáři také vyjádřili zájem o schopnost být externím systémům komunikovat iniciovat transakce vytvoření uživatelů, vytvoření smluv a aktualizace smlouvy na účetní kniha. Zatímco tato funkce není aktuálně dostupné ve verzi public preview, ukázky, která poskytuje tuto funkci lze nalézt v [ http://aka.ms/blockchain-workbench-integration-sample ](https://aka.ms/blockchain-workbench-integration-sample).

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

1.  Vytvořte nový **aplikace logiky Azure** na webu Azure Portal.
2.  Při otevření aplikace logiky Azure na portálu, se výzva k výběru aktivační události. Vyberte **Azure Event Grid – při výskytu události prostředku**.
3. Po zobrazení návrháře postupu provádění se výzva k přihlášení.
4. Vyberte předplatné. Prostředek jako **Microsoft.EventGrid.Topics**. Vyberte **název prostředku** z názvu zdroje ze skupiny prostředků Azure Blockchain Workbench.
5. Vyberte službu Event Grid ze skupiny prostředků Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Používání témat Service Bus pro oznámení

Témata služby Service Bus je možné informovat uživatele o události, ke kterým dochází v Blockchain Workbench. 

1.  Přejděte na Service Bus v rámci skupiny prostředků v aplikaci Workbench.
2.  Vyberte **témata**.
3.  Vyberte **externí aplikace workbench**.
4.  Vytvořte nový odběr tohoto tématu. Získáte klíče pro něj.
5.  Vytvořte program, který se přihlásí k odběru událostí z tohoto předplatného.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Přijímat zprávy služby Service Bus s Logic Apps

1. Vytvořte nový **aplikace logiky Azure** na webu Azure Portal.
2. Při otevření aplikace logiky Azure na portálu, se výzva k výběru aktivační události. Typ **služby Service Bus** do vyhledávacího pole a vyberte trigger vhodné pro typ interakce chcete mít službou Service Bus. Například **služby Service Bus – při doručení zprávy do odběru tématu (automatické dokončení)**.
3. Když se objeví návrháře postupu provádění, zadejte informace o připojení pro služby Service Bus.
4. Vyberte své předplatné a zadejte téma **externí aplikace workbench**.
5. Vyvíjejte logiku pro vaše aplikace, která využívá zprávy z této aktivační události.

## <a name="notification-message-reference"></a>Odkaz na zprávu oznámení

V závislosti na tom **OperationName**, zprávy s oznámením některou z následujících typů zpráv.

### <a name="accountcreated"></a>AccountCreated

Označuje, že nový účet byl požádán o přidají do zadaného řetězce.

| Název    | Popis  |
|----------|--------------|
| UserId  | ID uživatele, který byl vytvořen. |
| ChainIdentifier | Adresa uživatele, který byl vytvořen v síti blockchain. V Etherea, jde uživatele **v řetězu** adresu. |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

Označuje, že byla podána žádost Vložit či aktualizovat kontrakt na distribuované účetní knihy.

| Název | Popis |
|-----|--------------|
| ChainID | Jedinečný identifikátor pro řetězec přidružený k požadavku.|
| Blockid % | Jedinečný identifikátor pro blok na hlavní knihy.|
| ContractId | Jedinečný identifikátor pro kontrakt.|
| ContractAddress |       Adresa smlouvy na hlavní knihy.|
| TransactionHash  |     Hodnota hash transakce na hlavní knihy.|
| OriginatingAddress |   Adresa odesílatel požadavku dostane informaci transakce.|
| Název akce       |     Název akce.|
| IsUpdate        |      Určuje, zda jde o aktualizaci.|
| Parametry       |     Seznam objektů, které identifikují název, hodnotu a datový typ parametrů odesílat akci.|
| TopLevelInputParams |  V situacích, kdy kontrakt připojen k jedné nebo více jiných smluv jedná se parametry z nejvyšší úrovně kontraktu. |

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

Označuje, že požadavek byl proveden provádění akce u konkrétních kontraktu na distribuované účetní knihy.

| Název                     | Popis                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | Jedinečný identifikátor pro tuto akci kontraktu                                                                                                                                |
| ChainIdentifier          | Jedinečný identifikátor pro řetězce                                                                                                                                           |
| ID připojení             | Jedinečný identifikátor pro připojení                                                                                                                                      |
| UserChainIdentifier      | Adresa uživatele, který byl vytvořen v síti blockchain. V Etherea jde uživatele "v řetězci" adresu.                                                     |
| ContractLedgerIdentifier | Adresa kontraktu na hlavní knihy.                                                                                                                                        |
| WorkflowFunctionName     | Název funkce pracovního postupu.                                                                                                                                                |
| WorkflowName             | Název pracovního postupu.                                                                                                                                                         |
| WorkflowBlobStorageURL   | Adresa url smlouvy v úložišti objektů blob.                                                                                                                                      |
| ContractActionParameters | Parametry pro akce kontraktu.                                                                                                                                           |
| TransactionHash          | Hodnota hash transakce na hlavní knihy.                                                                                                                                    |
| Stav zřizování      | Aktuální stav zřizování akce.</br>0 – vytvořeno</br>1 – v procesu</br>2 – dokončení</br> Označuje dokončení potvrzení z hlavní knihy, které toto byl úspěšně přidán.                                               |
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

Označuje, že byl proveden požadavek aktualizace zůstatku uživatele na konkrétní distribuované účetní knihy.

> [!NOTE]
> Tato zpráva je vygenerována pouze pro tyto účetní knihy, které vyžadují financování účty.
> 

| Název    | Popis                              |
|---------|------------------------------------------|
| Adresa | Adresa uživatele, který byl financování. |
| Zůstatek | Vyrovnání zůstatek na účtu uživatele.         |
| ChainID | Jedinečný identifikátor pro řetězce.     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

Zpráva znamená, že byla podána žádost a přidejte do bloku na distribuované účetní knihy.

| Název           | Popis                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | Jedinečný identifikátor řetězce, do které byl přidán bloku.             |
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

Zpráva obsahuje podrobnosti o žádosti o přidání transakce na distribuované účetní knihy.

| Název            | Popis                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | Jedinečný identifikátor řetězce, do které byl přidán bloku.             |
| Blockid %         | Jedinečný identifikátor pro blok uvnitř Azure Blockchain Workbench. |
| TransactionHash | Hodnota hash transakce.                                           |
| Od            | Adresa odesílatel požadavku dostane informaci transakce.                      |
| Akce              | Adresa příjemce transakce.              |
| Hodnota           | Hodnota, která jsou součástí transakce.                                 |
| IsAppBuilderTx  | Určuje, zda jde Blockchain Workbench transakce.                         |

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

Poskytuje podrobné informace o přiřazení identifikátor řetězce pro kontrakt. Například v Etherea blockchain, adresu kontraktu na hlavní knihy.

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

## <a name="classes-used-by-message-types"></a>Třídy používané typy zpráv

### <a name="messagemodelbase"></a>MessageModelBase

Základní model pro všechny zprávy.

| Název          | Popis                          |
|---------------|--------------------------------------|
| OperationName | Název operace.           |
| ID žádosti     | Jedinečný identifikátor pro daný požadavek. |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

Obsahuje název, hodnotu a typ parametru.

| Název  | Popis                 |
|-------|-----------------------------|
| Název  | Název parametru  |
| Hodnota | Hodnota parametru. |
| Typ  | Typ parametru.  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

Obsahuje ID, název, hodnotu a typ vlastnosti.

| Název  | Popis                |
|-------|----------------------------|
| ID    | ID vlastnosti.    |
| Název  | Název vlastnosti.  |
| Hodnota | Hodnota vlastnosti |
| Typ  | Typ vlastnosti.  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vzory integrace inteligentní kontraktu](integration-patterns.md)