---
title: Získat další data, položky nebo záznamy se stránkování – Azure Logic Apps
description: Nastavit stránkování překročit výchozí limit velikosti stránky pro akce konektorů v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476538"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Získat další data, položky nebo záznamů pomocí stránkování v Azure Logic Apps

Při načítání dat, položky nebo záznamů pomocí konektor akce v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), může se zobrazit sad výsledků dotazu tak velká, že akce nemá všechny výsledky jsou vráceny ve stejnou dobu. Pomocí některé akce může překročit počet výsledků, které tento konektor výchozí velikost stránky. Akce v tomto případě vrátí jenom první stránka výsledků. Například výchozí velikost stránky pro konektor systému SQL Server **získat řádky** akce je 2048, ale může lišit v závislosti na dalších nastavení.

Některé akce umožňují zapnout *stránkování* nastavení tak, aby vaše aplikace logiky můžete načíst více výsledků až do limitu stránkování, ale vrátí výsledky jako jedna zpráva, po dokončení akce. Při použití stránkování, musí zadat *prahová hodnota* hodnotu, která je cílový počet výsledků, které chcete, aby akce, která vrátí. Tato akce načte výsledky dokud se nedosáhne určenou prahovou hodnotou. Když váš celkový počet položek, které je menší než zadaná prahová hodnota, tato akce načte všechny výsledky.

Zapnutí stránky načte nastavení stránkování výsledků na základě velikosti stránky konektor. Toto chování znamená, že v některých případech můžete získat další výsledky než zadaná prahová hodnota. Například při použití SQL serveru **získat řádky** akce, která podporuje stránkování nastavení:

* Výchozí velikost stránky akce je 2048 záznamů na stránku.
* Předpokládejme, že máte 10 000 záznamů a jako minimální zadat 5000 záznamů.
* Stránkování získá stránky záznamů, chci dostat alespoň zadané minimum, tato akce vrátí 6144 záznamů (3 stránky x 2048 záznamů), ne 5000 záznamů.

Tady je seznam s jen některé z konektorů, kde může přesáhnout výchozí velikost stránky pro určité akce:

* [Azure Blob Storage](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte ještě předplatné Azure [zaregistrovat si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky a akce, ve které chcete zapnout stránkování. Pokud nemáte aplikace logiky, přečtěte si téma [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Zapnout stránkování

Pokud chcete zjistit, zda akce podporuje stránkování v návrháři aplikace logiky, zkontrolujte nastavení na akci pro **stránkování** nastavení. Tento příklad ukazuje, jak zapnout stránkování v systému SQL Server **získat řádky** akce.

1. V pravém horním rohu akce, klikněte na trojtečku (**...** ) tlačítko a vyberte **nastavení**.

   ![Otevřete nastavení akce](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Pokud tato akce podporuje stránkování, se zobrazí akce **stránkování** nastavení.

1. Změnit **stránkování** nastavení z **vypnout** k **na**. V **prahová hodnota** vlastnost, zadejte celočíselnou hodnotu pro cílový počet výsledků, které chcete, aby akce, která vrátí.

   ![Zadejte minimální počet s vrácenými výsledky](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Jakmile budete připraveni, zvolte **provádí**.

## <a name="workflow-definition---pagination"></a>Definice pracovního postupu - stránkování

Když zapnete stránkování pro akci, která podporuje tuto funkci, obsahuje definice pracovního postupu aplikace logiky `"paginationPolicy"` vlastnost spolu s `"minimumItemCount"` vlastnost v této akci `"runtimeConfiguration"` vlastnosti, například:

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>Získat podporu

Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
