---
title: Plány a fakturace
description: Další informace o plánech a fakturaci služby Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: e821036ff4ddb5a9786bc4f4537bb81539ab2c87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78898479"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plány a fakturace pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

## <a name="job-collection-plans"></a>Plány kolekce úloh

Kolekce úloh ve službě Azure Scheduler obsahuje určitý počet úloh. Kolekce úloh je fakturovatelná entita a je dostupná v plánech Standard, P10 Premium a P20 Premium, které jsou popsané tady: 

| Plán kolekce úloh | Maximální počet úloh na kolekci | Maximální opakování | Maximální počet kolekcí úloh na předplatné | Omezení | 
|:--- |:--- |:--- |:--- |:--- |
| **Standardní** | 50 úloh na kolekci | Jedna za minutu. Úlohy nejde spouštět častěji než jednou za minutu. | Každé předplatné Azure může mít až 100 standardních kolekcí úloh. | Přístup k úplné sadě funkcí Scheduleru | 
| **P10 Premium** | 50 úloh na kolekci | Jedna za minutu. Úlohy nejde spouštět častěji než jednou za minutu. | Každé předplatné Azure může mít až 10 000 kolekcí úloh P10 Premium. Pokud máte další kolekce, <a href="mailto:wapteams@microsoft.com">kontaktujte nás</a>. | Přístup k úplné sadě funkcí Scheduleru |
| **P20 Premium** | 1000 úloh na kolekci | Jedna za minutu. Úlohy nejde spouštět častěji než jednou za minutu. | Každé předplatné Azure může mít až 5 000 kolekcí úloh P20 Premium. Pokud máte další kolekce, <a href="mailto:wapteams@microsoft.com">kontaktujte nás</a>. | Přístup k úplné sadě funkcí Scheduleru |
|||||| 

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny Scheduleru](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Upgradovat nebo downgradovat plány

Plán kolekce úloh můžete kdykoli upgradovat nebo downgradovat napříč plány Standard, P10 Premium a P20 Premium.

## <a name="active-status-and-billing"></a>Aktivní stav a fakturace

Kolekce úloh jsou vždycky aktivní, pokud celé předplatné Azure nepřejde do dočasného zakázaného stavu kvůli problémům s fakturací. A i když můžete zakázat všechny úlohy v kolekci úloh prostřednictvím jediné operace, tato akce nezmění stav fakturace kolekce úloh, takže se kolekce úloh *stále* účtuje. Prázdné kolekce úloh se považují za aktivní a účtují se.

Aby se zajistilo, že se kolekce úloh účtuje, je nutné ji odstranit.

## <a name="standard-billable-units"></a>Fakturovatelné jednotky úrovně Standard

Standardní fakturovatelná jednotka může mít až 10 standardních kolekcí úloh. Vzhledem k tomu, že standardní kolekce úloh může mít až 50 úloh na jednu kolekci, jedna standardní fakturační jednotka umožňuje, aby vaše předplatné Azure mělo až 500 úlohy, nebo až skoro 22 *milionů* spuštění úloh měsíčně. Tento seznam vysvětluje, jak se účtují na základě různých čísel standardních kolekcí úloh:

* Pokud máte mezi 1 a 10 standardními kolekcemi úloh, účtuje se vám jedna standardní fakturační jednotka. 

* Pokud máte mezi 11 a 20 standardními kolekcemi úloh, účtuje se vám ze dvou standardních fakturačních jednotek. 

* Pokud máte mezi 21 a 30 standardními kolekcemi úloh, účtuje se vám tři standardní fakturační jednotky atd.

## <a name="p10-premium-billable-units"></a>Fakturovatelné jednotky P10 Premium

Fakturovatelná jednotka P10 Premium může mít až 10 000 kolekcí úloh P10 Premium. Vzhledem k tomu, že kolekce úloh P10 Premium může mít až 50 úloh na jednu kolekci, pak jedna fakturační jednotka P10 Premium umožní vašemu předplatnému Azure až 500 000 úlohy nebo až o 22 *miliard* provádění úloh za měsíc. 

Kolekce úloh P10 Premium poskytují stejné možnosti jako standardní kolekce úloh, ale nabízejí cenové přerušení pro aplikace, které vyžadují mnoho kolekcí úloh a poskytují větší škálovatelnost. Tento seznam vysvětluje, jak se fakturuje na základě různých počtů kolekcí úloh P10 Premium:

* Pokud máte mezi 1 a 10 000 P10mi kolekcemi úloh Premium, bude se vám účtovat jedna fakturační jednotka služby P10 Premium. 

* Pokud máte 10 001 a 20 000 P10ch úloh Premium, budou se vám účtovat 2 fakturační jednotky P10 Premium atd.

## <a name="p20-premium-billable-units"></a>Fakturovatelné jednotky P20 Premium

Fakturovatelná jednotka P20 Premium může mít až 5 000 kolekcí úloh P20 Premium. Vzhledem k tomu, že kolekce úloh P20 Premium může mít až 1 000 úloh na jednu kolekci úloh, pak jedna fakturační jednotka P20 Premium umožňuje, aby vaše předplatné Azure mělo až 5 000 000 úlohy, nebo až téměř 220 *miliard* úloh za měsíc.

Kolekce úloh P20 Premium poskytují stejné možnosti jako kolekce úloh P10 Premium, ale také podporují větší počet úloh na kolekci a vyšší celkový počet úloh, které jsou celkově větší než P10 Premium a poskytují větší škálovatelnost.

## <a name="plan-comparison"></a>Porovnání plánu

* Pokud máte více než 100 standardních kolekcí úloh (10 standardních fakturačních jednotek), můžete získat lepší přehled o všech kolekcích úloh v plánu Premium.

* Pokud máte jednu standardní kolekci úloh a jednu kolekci úloh Premium, bude se vám účtovat jedna standardní fakturační jednotka *a* jedna fakturační jednotka úrovně Premium.

  Služba plánovače vychází z počtu aktivních kolekcí úloh, které jsou buď Standard, nebo Premium.

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)