---
title: Získání dalších položek nebo záznamů pomocí stránkování
description: Nastavení stránkování tak, aby překročilo výchozí limit velikosti stránky pro akce konektoru v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 75d9660eb35b5d7ddc644d177c11ae489e2853dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792107"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>Získání dalších dat, položek nebo záznamů pomocí stránkování v Aplikacích Logika Azure

Při načítání dat, položek nebo záznamů pomocí akce konektoru v [Azure Logic Apps](../logic-apps/logic-apps-overview.md), může získat sady výsledků tak velké, že akce nevrátí všechny výsledky ve stejnou dobu. U některých akcí může počet výsledků překročit výchozí velikost stránky konektoru. V tomto případě akce vrátí pouze první stránku výsledků. Například výchozí velikost stránky pro akce **Získat řádky** konektoru SQL Server je 2048, ale může se lišit v závislosti na jiných nastaveních.

Některé akce umožňují zapnout nastavení *stránkování* tak, aby aplikace logiky můžete načíst další výsledky až do limitu stránkování, ale vrátit tyto výsledky jako jednu zprávu po dokončení akce. Při použití stránkování je nutné zadat *prahovou* hodnotu, což je cílový počet výsledků, které má být akce vrácena. Akce načte výsledky až do dosažení zadané prahové hodnoty. Pokud je celkový počet položek menší než zadaná prahová hodnota, akce načte všechny výsledky.

Zapnutínastavení stránkování načte stránky výsledků na základě velikosti stránky spojnice. Toto chování znamená, že někdy můžete získat více výsledků, než je zadaná prahová hodnota. Například při použití akce Sql Server **Získat řádky,** která podporuje nastavení stránkování:

* Výchozí velikost stránky akce je 2048 záznamů na stránku.
* Předpokládejme, že máte 10 000 záznamů a jako minimum zadejte 5000 záznamů.
* Stránkování získá stránky záznamů, takže chcete-li získat alespoň zadané minimum, akce vrátí 6144 záznamů (3 stránky x 2048 záznamů), nikoli 5000 záznamů.

Zde je seznam s pouze některé konektory, kde můžete překročit výchozí velikost stránky pro konkrétní akce:

* [Úložiště objektů blob Azure](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Aplikace excel](https://docs.microsoft.com/connectors/excel/)
* [Protokol HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Aplikace logiky a akce, kde chcete zapnout stránkování. Pokud nemáte aplikaci logiky, přečtěte si [úvodní příručku: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="turn-on-pagination"></a>Zapnutí stránkování

Chcete-li zjistit, zda akce podporuje stránkování v Návrháři aplikace logiky, zkontrolujte nastavení akce pro nastavení **stránkování.** Tento příklad ukazuje, jak zapnout stránkování v akci **Získat řádky** serveru SQL Server.

1. V pravém horním rohu akce zvolte tlačítko elipsy (**...**) a vyberte **Nastavení**.

   ![Otevření nastavení akce](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   Pokud akce podporuje stránkování, akce zobrazí nastavení **Stránkování.**

1. Změňte nastavení **stránkování** z **vypnuto** **na Zapnuto**. Ve vlastnosti **Threshold** zadejte celou hodnotu pro cílový počet výsledků, které má být akce vrácena.

   ![Zadejte minimální počet výsledků, které mají být vráceny.](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. Až budete připraveni, zvolte **Hotovo**.

## <a name="workflow-definition---pagination"></a>Definice pracovního postupu - stránkování

Když zapnete stránkování pro akci, která podporuje tuto funkci, definice `"paginationPolicy"` pracovního postupu `"minimumItemCount"` aplikace logiky `"runtimeConfiguration"` zahrnuje vlastnost spolu s vlastností ve vlastnosti této akce, například:

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
