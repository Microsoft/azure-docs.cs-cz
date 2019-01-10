---
title: Vazba externí tabulky pro službu Azure Functions (experimentální)
description: Používání vazeb externí tabulky ve službě Azure Functions
services: functions
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: cshoe
ms.openlocfilehash: 38f0d03698ff8b11e1a6a06dc29102f407e0e90f
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156863"
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Vazba externí tabulky pro službu Azure Functions (experimentální)

Tento článek vysvětluje, jak fungují s tabulkovými daty na poskytovatelé SaaS, jako jsou SharePoint a Dynamics ve službě Azure Functions. Azure Functions podporuje vstupní a výstupní vazby pro externí tabulky.

> [!IMPORTANT]
> Vazba externí tabulky je experimentální a může být nikdy nedorazí stav všeobecně dostupná (GA). Je zahrnuta pouze v Azure Functions 1.x a nejsou žádné plány přidat do služby Azure Functions 2.x. Pro scénáře, které vyžadují přístup k datům v poskytovatelé SaaS, zvažte použití [aplikace logiky, které volají do funkce](functions-twitter-email.md).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>Připojení API

Vazby tabulek využívat externí připojení rozhraní API k ověření pomocí poskytovatelů SaaS třetích stran. 

Při přiřazování vazby můžete vytvořit nové připojení rozhraní API nebo použít existující připojení rozhraní API v rámci stejné skupiny prostředků.

### <a name="available-api-connections-tables"></a>K dispozici připojení rozhraní API (tabulky)

|Spojovací čára|Trigger|Vstup|Výstup|
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
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](https://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> Připojení externí tabulky lze použít také v [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="creating-an-api-connection-step-by-step"></a>Vytváří se připojení k rozhraní API: krok za krokem

1. Na stránce webu Azure portal pro vaši aplikaci function app, vyberte symbol plus (**+**) vytvořit funkci.

1. V **scénář** vyberte **experimentální**.

1. Vyberte **externí tabulky**.

1. Vyberte jazyk.

2. V části **připojení externí tabulky**, vyberte existující připojení nebo **nové**.

1. Pro nové připojení, nakonfigurujte nastavení a vyberte **Authorize**.

1. Vyberte **vytvořit** vytvořte novou funkci.

1. Vyberte **integrovat > externí tabulky**.

1. Konfigurace připojení pro použití cílovou tabulku. Tato nastavení se liší mezi poskytovatelé SaaS. Příklady jsou zahrnuty v následující části.

## <a name="example"></a>Příklad:

Tento příklad se připojí k tabulku s názvem "Kontakt" s Id, příjmení a FirstName sloupce. Kód obsahuje entity Kontakt v tabulce a protokoly jména a příjmení.

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

### <a name="sql-server-data-source"></a>Zdroj dat SQL serveru

K vytvoření tabulky v SQL serveru pro použití v tomto příkladu, tady je skript. `dataSetName` je "Výchozí".

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

### <a name="google-sheets-data-source"></a>Zdroj dat v tabulkách Google

Pokud chcete vytvořit tabulku a použít v tomto příkladu v dokumenty Google, vytvořte tabulku s listem s názvem `Contact`. Zobrazovaný název tabulky nelze použít tento konektor. Požadavky vnitřní název (tučně) má být použit jako dataSetName, například: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** Přidat názvy sloupců `Id`, `LastName`, `FirstName` na první řádek, pak naplnění dat v dalších řádcích.

### <a name="salesforce"></a>Salesforce

Chcete-li použít tento příklad se Salesforce, `dataSetName` je "Výchozí".

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru.

|Vlastnost Function.JSON | Popis|
|---------|----------------------|
|**type** | Musí být nastaveno na `apiHubTable`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal.|
|**direction** | Musí být nastaveno na `in`. Tato vlastnost je nastavena automaticky, když vytvoříte aktivační událost na webu Azure Portal. |
|**Jméno** | Název proměnné, která představuje položku událost v kódu funkce. | 
|**připojení**| Určuje nastavení aplikace, který uchovává připojovací řetězec API. Nastavení aplikace, které se vytvoří automaticky při přidání připojení k rozhraní API do integrace uživatelského rozhraní.|
|**dataSetName**|Název datové sady, který obsahuje tabulku ke čtení.|
|**tableName**|Název tabulky|
|**ID subjektu**|Musí být prázdná pro vazby tabulky.

Tabulkové konektor poskytuje datové sady a obsahuje každou sadu dat tabulky. Název výchozí sadu dat je "Výchozí". Názvy pro datovou sadu a tabulky v různých poskytovatelů SaaS jsou uvedeny níže:

|Spojovací čára|Datová sada|Table|
|:-----|:---|:---| 
|**SharePoint**|Web|Seznam služby SharePoint
|**SQL**|Databáze|Table 
|**Tabulky Google.**|Tabulka|List 
|**Excel**|Excelový soubor|Tabulka 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
