---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady pro zabezpečení služby Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 97c9480d6f2b75d83252bfb6410d7b5f946757ef
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630649"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Udělení přístupu k datům prostředí Time Series Insights pomocí webu Azure Portal

V prostředích Time Series Insights jsou dva nezávislé typy zásad přístupu:

* Zásady přístupu ke správě
* Zásady přístupu k datům

Oba typy zásad udělují objektům zabezpečení Azure Active Directory (uživatelům a aplikacím) různá oprávnění ke konkrétnímu prostředí. Objekty zabezpečení (uživatelé a aplikace) musí patřit do active directory (označuje se jako Azure tenant) přidružené k předplatnému, které obsahuje prostředí.

Zásady přístupu ke správě udělují oprávnění související s konfigurací prostředí, jako je například
*   vytvoření nebo odstranění prostředí, zdrojů událostí nebo referenčních datových sad a
*   správa zásad přístupu k datům.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

Tyto dva typy zásad umožňují jasné oddělení přístupu ke správě prostředí od přístupu k datům v prostředí. Například je možné nastavit prostředí tak, aby vlastník nebo Tvůrce prostředí se odebere přístup k datům. Kromě toho uživatele a služby, které můžou číst data z prostředí může udělit přístup ke konfiguraci prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům
Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Typ **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání. 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.
   
4. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.
  ![Správa zdroje Time Series Insights – prostředí](media/data-access/getstarted-grant-data-access1.png)

5. Vyberte **vybrat uživatele**.  Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr. 

   ![Správa zdroje Time Series Insights – přidat](media/data-access/getstarted-grant-data-access2.png)

6. Vyberte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:
   - Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí. 
   - V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

   Vyberte **Ok** k potvrzení volby role.

   ![Správa zdroje Time Series Insights – vybrat uživatele](media/data-access/getstarted-grant-data-access3.png)

8. Vyberte **Ok** v **vybrat roli uživatele** stránky.

   ![Správa zdroje Time Series Insights – vybrat roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

   ![Správa zdroje Time Series Insights – výsledky](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Další postup
* Přečtěte si [přidání zdroje událostí centra událostí do prostředí Azure Time Series Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji události.
* Zobrazte své prostředí v [Průzkumníka služby Time Series Insights](https://insights.timeseries.azure.com).
