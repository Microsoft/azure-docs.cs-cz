---
title: Konfigurace zabezpečení pro přístup k a spravovat Azure časové řady Insights | Microsoft Docs
description: Tento článek popisuje způsob konfigurace zabezpečení a oprávnění jako přístup pro správu zásad a přístupu k datům zásady zabezpečit Statistika Azure časové řady.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: 4306d22f03faa55fb6fc8be1a359aea3410e8038
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653806"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Udělení přístupu k datům prostředí Time Series Insights pomocí webu Azure Portal

V prostředích Time Series Insights jsou dva nezávislé typy zásad přístupu:

* Zásady přístupu ke správě
* Zásady přístupu k datům

Oba typy zásad udělují objektům zabezpečení Azure Active Directory (uživatelům a aplikacím) různá oprávnění ke konkrétnímu prostředí. Objekty (uživatelé a aplikace) musí patřit do služby active directory (označuje se jako ke klientovi Azure) přidruženou k odběru, který obsahuje prostředí.

Zásady přístupu ke správě udělují oprávnění související s konfigurací prostředí, jako je například
*   vytvoření nebo odstranění prostředí, zdrojů událostí nebo referenčních datových sad a
*   správa zásad přístupu k datům.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

Tyto dva typy zásad umožňují jasné oddělení přístupu ke správě prostředí od přístupu k datům v prostředí. Například je možné nastavit prostředí tak, aby vlastníka nebo autora prostředí se odebere z přístup k datům. Kromě toho uživatelů a služeb, které jsou povoleny číst data z prostředí udělit přístup ke konfiguraci prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům
Postupujte podle těchto kroků k udělení přístupu k datům pro objekt zabezpečení uživatele:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Statistika časové řady. Typ **časové řady** v **vyhledávání** pole. Vyberte **časové řady prostředí** ve výsledcích hledání. 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.
   
4. Vyberte **zásady přístupu k datům**, pak vyberte **+ přidat**.
  ![Spravovat zdroji časové řady Insights - prostředí](media/data-access/getstarted-grant-data-access1.png)

5. Vyberte **vyberte uživatele**.  Vyhledejte uživatelského jména nebo e-mailové adresy k vyhledání uživatele, které chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr. 

   ![Správa zdroje Time Series Insights – přidat](media/data-access/getstarted-grant-data-access2.png)

6. Vyberte **vyberte role**. Zvolte odpovídající přístup roli pro uživatele:
   - Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky uložit dotazy a perspektivy s ostatními uživateli prostředí. 
   - Jinak vyberte možnost **čtečky** povolit uživatele dotaz na data v prostředí a ukládejte dotazy za osobní (není sdílený) v prostředí.

   Vyberte **Ok** potvrďte volbu role.

   ![Správa zdroje Time Series Insights – vybrat uživatele](media/data-access/getstarted-grant-data-access3.png)

8. Vyberte **Ok** v **vybrat roli uživatele** stránky.

   ![Správa zdroje Time Series Insights – vybrat roli](media/data-access/getstarted-grant-data-access4.png)

9. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

   ![Správa zdroje Time Series Insights – výsledky](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Další postup
* Další informace [přidání zdroje událostí centra událostí do prostředí Azure časové řady Insights](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Odesílání událostí](time-series-insights-send-events.md) ke zdroji událostí.
* Zobrazit prostředí v [explorer časové řady Insights](https://insights.timeseries.azure.com).
