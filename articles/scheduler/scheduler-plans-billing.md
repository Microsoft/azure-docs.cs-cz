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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898479"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plány a fakturace pro Plánovač Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

## <a name="job-collection-plans"></a>Plány shromažďování pracovních míst

V Plánovačazure kolekce úloh obsahuje určitý počet úloh. Kolekce úloh je fakturovatelná entita a je dodávána v plánech Standard, P10 Premium a P20 Premium, které jsou popsány zde: 

| Plán sběru úloh | Maximální počet úloh na kolekci | Maximální opakování | Maximální počet kolekcí úloh na jedno předplatné | Omezení | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 úloh na kolekci | Jeden za minutu. Nelze spouštět úlohy častěji než jednu za minutu. | Každé předplatné Azure může mít až 100 standardních kolekcí úloh. | Přístup k úplné sadě funkcí Plánovače | 
| **P10 Premium** | 50 úloh na kolekci | Jeden za minutu. Nelze spouštět úlohy častěji než jednu za minutu. | Každé předplatné Azure může mít až 10 000 kolekcí úloh P10 Premium. Pro více sbírek <a href="mailto:wapteams@microsoft.com">nás kontaktujte</a>. | Přístup k úplné sadě funkcí Plánovače |
| **P20 Premium** | 1000 pracovních míst na kolekci | Jeden za minutu. Nelze spouštět úlohy častěji než jednu za minutu. | Každé předplatné Azure může mít až 5 000 kolekcí úloh P20 Premium. Pro více sbírek <a href="mailto:wapteams@microsoft.com">nás kontaktujte</a>. | Přístup k úplné sadě funkcí Plánovače |
|||||| 

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v [tématu Scheduler Pricing](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Upgrade nebo downgrade plány

Kdykoli můžete upgradovat nebo downgrade plán kolekce úloh v rámci plánů Standard, P10 Premium a P20 Premium.

## <a name="active-status-and-billing"></a>Aktivní stav a fakturace

Kolekce úloh jsou vždy aktivní, pokud celé vaše předplatné Azure přejde do dočasného zakázaného stavu kvůli problémům s fakturací. A i když můžete zakázat všechny úlohy v kolekci úloh prostřednictvím jedné operace, tato akce nezmění stav fakturace kolekce úloh, takže kolekce úloh se *stále* účtuje. Prázdné kolekce úloh jsou považovány za aktivní a jsou fakturovány.

Chcete-li se ujistit, že kolekce úloh není fakturována, je nutné ji odstranit.

## <a name="standard-billable-units"></a>Standardní fakturovatelné jednotky

Standardní fakturovatelná jednotka může mít až 10 standardních kolekcí úloh. Vzhledem k tomu, že kolekce úloh Standard může mít až 50 úloh na kolekci, jedna standardní fakturační jednotka umožňuje vašemu předplatnému Azure mít až 500 úloh nebo až téměř 22 *milionů* spuštění úloh za měsíc. Tento seznam vysvětluje, jak se vám účtují na základě různých počtů standardních kolekcí úloh:

* Pokud máte 1 až 10 standardních kolekcí úloh, bude se vám účtovat jedna standardní fakturační jednotka. 

* Pokud máte 11 až 20 standardních kolekcí úloh, budou vám účtovány dvě standardní fakturační jednotky. 

* Pokud máte 21 až 30 standardních kolekcí úloh, budou se vám účtovat tři standardní fakturační jednotky atd.

## <a name="p10-premium-billable-units"></a>Prémiové fakturovatelné jednotky P10

Prémiová fakturovatelná jednotka P10 může mít až 10 000 prémiových kolekcí. Vzhledem k tomu, že kolekce úloh P10 Premium může mít až 50 úloh na kolekci, jedna prémiová fakturační jednotka P10 umožňuje vašemu předplatnému Azure mít až 500 000 úloh nebo až téměř 22 *miliard* spuštění úloh za měsíc. 

Kolekce úloh P10 Premium poskytují stejné možnosti jako standardní kolekce úloh, ale nabízejí cenovou přestávku pro aplikace, které vyžadují mnoho kolekcí úloh a poskytují větší škálovatelnost. Tento seznam vysvětluje, jak se vám účtují na základě různých počtů kolekcí pracovních míst P10 Premium:

* Pokud máte 1 až 10 000 kolekcí prvotřídních pracovních míst P10, bude vám účtována jedna prémiová fakturační jednotka P10. 

* Pokud máte 10 001 až 20 000 prémiových kolekcí pracovních míst P10, bude vám účtováno 2 prémiové fakturační jednotky P10 atd.

## <a name="p20-premium-billable-units"></a>Prémiové fakturovatelné jednotky P20

Prémiová fakturovatelná jednotka P20 může mít až 5 000 prémiových kolekcí. Vzhledem k tomu, že kolekce úloh P20 Premium může mít až 1 000 úloh na kolekci úloh, jedna prémiová fakturační jednotka P20 umožňuje vašemu předplatnému Azure mít až 5 000 000 úloh nebo až téměř 220 *miliard* úloh za měsíc.

Kolekce úloh P20 Premium poskytují stejné možnosti jako kolekce úloh P10 Premium, ale také podporují větší počet úloh na kolekci a větší celkový počet úloh celkově než P10 Premium, což poskytuje větší škálovatelnost.

## <a name="plan-comparison"></a>Porovnání plánu

* Pokud máte více než 100 standardních kolekcí úloh (10 standardních fakturačních jednotek), můžete získat lepší nabídku tím, že budete mít všechny kolekce úloh v prémiovém plánu.

* Pokud máte jednu kolekci úloh Standard a jednu kolekci úloh Premium, bude se vám účtovat jedna standardní fakturační jednotka *a* jedna prémiová fakturační jednotka.

  Účty služeb plánovače na základě počtu aktivních kolekcí úloh, které jsou standardní nebo prémiové.

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)