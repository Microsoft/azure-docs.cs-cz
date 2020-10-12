---
title: Získání dalších položek nebo záznamů se stránkováním
description: Nastavte přestránkování tak, aby překročila výchozí limit velikosti stránky pro akce konektoru v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090259"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Získání dalších dat, položek nebo záznamů pomocí stránkování v Azure Logic Apps

Když načtete data, položky nebo záznamy pomocí akce konektoru v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), můžete získat sady výsledků tak velké, že akce nevrátí všechny výsledky ve stejnou dobu. U některých akcí může počet výsledků přesáhnout výchozí velikost stránky konektoru. V takovém případě akce vrátí pouze první stránku výsledků. Například výchozí velikost stránky pro akci **získat řádky** SQL serverového konektoru je 2048, ale může se lišit v závislosti na dalších nastaveních.

Některé akce umožňují zapnout nastavení *stránkování* , aby vaše aplikace logiky mohla získat více výsledků až po limit stránkování, ale po dokončení akce vrátí tyto výsledky jako jednu zprávu. Při použití stránkování je nutné zadat *prahovou* hodnotu, což je cílový počet výsledků, který má akce vrátit. Akce načte výsledky, dokud nedosáhne zadané prahové hodnoty. Pokud je celkový počet položek menší, než je zadaná prahová hodnota, akce načte všechny výsledky.

Zapnutí nastavení stránkování načítá stránky výsledků na základě velikosti stránky spojnice. Toto chování znamená, že v některých případech můžete získat více výsledků, než je zadaná prahová hodnota. Například při použití akce SQL Server **získat řádky** , která podporuje nastavení stránkování:

* Výchozí velikost stránky akce je 2048 záznamů na stránku.
* Předpokládejme, že máte 10 000 záznamů a jako minimum zadáte 5000 záznamů.
* Stránkování získá stránky záznamů, takže pokud chcete získat alespoň stanovenou hodnotu, vrátí tato akce 6144 záznamů (3 stránky x 2048 záznamů), 5000 nikoli záznamy.

Tady je seznam s pouze některými konektory, kde můžete překročit výchozí velikost stránky pro konkrétní akce:

* [Azure Blob Storage](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle Database](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky a akce, u kterých chcete zapnout stránkování. Pokud nemáte aplikaci logiky, přečtěte si [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Zapnout stránkování

Pokud chcete zjistit, jestli akce podporuje stránkování v návrháři aplikace logiky, Projděte si nastavení akce pro nastavení **stránkování** . Tento příklad ukazuje, jak zapnout stránkování v akci SQL Server **načíst řádky** .

1. V pravém horním rohu akce zvolte tlačítko se třemi tečkami (**...**) a vyberte **Nastavení**.

   ![Otevřete nastavení akce.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Pokud akce podporuje stránkování, akce zobrazí nastavení **stránkování** .

1. Změňte nastavení **stránkování** z **vypnuto** na **zapnuto**. Do vlastnosti **prahová** hodnota zadejte celočíselnou hodnotu pro cílový počet výsledků, který má akce vrátit.

   ![Zadejte minimální počet výsledků, které se mají vrátit.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Až budete připraveni, vyberte **Hotovo**.

## <a name="workflow-definition---pagination"></a>Definice pracovního postupu – stránkování

Když zapnete stránkování pro akci, která tuto funkci podporuje, zahrnuje definice pracovního postupu vaší aplikace logiky `"paginationPolicy"` vlastnost spolu s `"minimumItemCount"` vlastností vlastnosti této akce `"runtimeConfiguration"` , například:

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

Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](/answers/topics/azure-logic-apps.html).
