---
title: Externí tabulky vazby pro Azure Functions (experimentální)
description: Používání vazeb externí tabulky v Azure Functions
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2018
ms.locfileid: "27613277"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Externí tabulky vazby pro Azure Functions (experimentální)

Tento článek vysvětluje, jak pracovat s tabulková data na poskytovatelů SaaS, například služby Sharepoint a Dynamics v Azure Functions. Azure Functions podporuje vstup a výstup vazby pro externí tabulky.

> [!IMPORTANT]
> Externí tabulky vazba je experimentální a může být nikdy dosáhne stavu obecně k dispozici (GA). Je zahrnuta pouze v Azure funguje 1.x, a neexistují žádné plány, které chcete přidat do Azure Functions 2.x. Pro scénáře, které vyžadují přístup k datům ve zprostředkovatelích SaaS, zvažte použití [logiku aplikace, které volají do funkce](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Připojení rozhraní API

Tabulka vazby využívat externí připojení rozhraní API k ověřování pro poskytovatele třetích stran SaaS. 

Při přiřazování vazbu můžete vytvořit nové připojení k rozhraní API nebo použít stávající připojení k rozhraní API v rámci stejné skupině prostředků.

### <a name="available-api-connections-tables"></a>Dostupná připojení rozhraní API (tabulky)

|Konektor|Trigger|Vstup|Výstup|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 pro operace](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Tabulky Google](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 pro Finance](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Běžné datové služby](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Externí připojení tabulky lze použít také v [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Vytvoření připojení k rozhraní API: krok za krokem

1. V Azure stránky portálu pro funkce aplikace, vyberte znaménko plus (**+**) vytvořit funkci.

1. V **scénář** vyberte **experimentální**.

1. Vyberte **externí tabulky**.

1. Vyberte jazyk.

2. V části **připojení externí tabulky**vyberte existující připojení nebo **nové**.

1. Pro nové připojení, nakonfigurujte nastavení a vyberte **Authorize**.

1. Vyberte **vytvořit** funkci vytvoříte.

1. Vyberte **integrovat > externí tabulky**.

1. Nakonfigurujte připojení používat, cílová tabulka. Tato nastavení se bude lišit mezi poskytovatelů SaaS. Příklady jsou zahrnuty v následující části.

## <a name="example"></a>Příklad:

Tento příklad se připojí k tabulku s názvem "Kontakt" s Id, FirstName a LastName sloupce. Kód zobrazí entity kontaktů v tabulce a protokoly první a poslední názvy.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

Tady je kód skriptu jazyka C#:

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>Zdroj dat systému SQL Server

Pokud chcete vytvořit tabulku v systému SQL Server, chcete-li použít v tomto příkladu, tady je skript. `dataSetName`je "default".

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Zdroj dat Google listy

Pokud chcete vytvořit tabulku, aby použít v tomto příkladu v Google dokumentace, vytvořit tabulku s na listu s názvem `Contact`. Zobrazovaný název tabulky nelze použít konektor. Interní název (tučným písmem) potřebám má být použit jako dataSetName, například: `docs.google.com/spreadsheets/d/`  **`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**  přidat názvy sloupců `Id`, `LastName`, `FirstName` na první řádek, pak naplnění dat na Další řádky.

### <a name="salesforce"></a>Salesforce

Použití tohoto příkladu s Salesforce, `dataSetName` je "default".

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|----------------------|
|**Typ** | musí být nastavena na `apiHubTable`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure.|
|**směr** | musí být nastavena na `in`. Tato vlastnost nastavena automaticky při vytváření aktivační události na portálu Azure. |
|**Jméno** | Název proměnné, která představuje položku událostí v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, která ukládá připojovací řetězec rozhraní API. Nastavení aplikace je vytvořena automaticky při přidání připojení k rozhraní API v integrací uživatelského rozhraní.|
|**dataSetName**|Název datové sady, který obsahuje tabulku, kterou chcete číst.|
|**Název tabulky**|Název tabulky|
|**entityId**|Musí být u vazeb tabulky prázdný.

Tabulkové konektor poskytuje datových sad a každé datové sady obsahuje tabulky. Název výchozí sadu dat je "default". Názvy pro datovou sadu a tabulky v různých zprostředkovatelů SaaS jsou uvedeny níže:

|Konektor|Datová sada|Table|
|:-----|:---|:---| 
|**SharePoint**|Web|Seznam serveru SharePoint
|**SQL**|Databáze|Table 
|**List Google**|Tabulka|List 
|**Excel**|Excelový soubor|Tabulka 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
