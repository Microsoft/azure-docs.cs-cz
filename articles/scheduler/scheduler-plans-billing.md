---
title: Plány a fakturace ve Azure Scheduler
description: Plány a fakturace ve Azure Scheduler
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: b25e97b0f0d0b6f63134a774856eb7ec8f77b679
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Plány a fakturace ve Azure Scheduler
## <a name="job-collection-plans"></a>Plány kolekce úloh
Kolekce úloh jsou fakturovatelné entity ve službě Azure Scheduler. Kolekce úloh obsahují určitý počet úloh a mají tři plány – standardní, P10 Premium a P20 Premium –, které jsou popsány níže.

| **Plán kolekce úloh** | **Maximální počet úloh na kolekci úloh** | **Maximální počet opakování** | **Kolekce maximální počet úloh na předplatné** | **Omezení** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |50 úloh na kolekci úloh |Jednou za minutu. Nelze provést úlohy častěji než jednou za minutu |Předplatné je povolená na 100 kolekce standardní úloh |Přístup k úplná sada funkcí služby plánovače |
| **P10 Premium** |50 úloh na kolekci úloh |Jednou za minutu. Nelze provést úlohy častěji než jednou za minutu |Předplatné je povolená kolekce úloh až 10 000 P10 úrovně Premium. <a href="mailto:wapteams@microsoft.com">Kontaktujte nás</a> Další informace. |Přístup k úplná sada funkcí služby plánovače |
| **P20 Premium** |1000 úloh na kolekci úloh |Jednou za minutu. Nelze provést úlohy častěji než jednou za minutu |Předplatné je povolená kolekce úloh až 10 000 P20 Premium. <a href="mailto:wapteams@microsoft.com">Kontaktujte nás</a> Další informace. |Přístup k úplná sada funkcí služby plánovače |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Upgrady a dírám plány kolekce úloh
Můžete upgradovat nebo starší verzi plán kolekce úloh kdykoli mezi standardní, P10 Premium a P20 Premium plány.

## <a name="billing-and-azure-plans"></a>Plány fakturace a Azure
Pokud máte více než 100 kolekce standardní úloh (10 standardní fakturace jednotky), je lepší pozornosti mít všechny kolekce úloh v plánu premium.

Pokud máte jednu kolekci standardní úlohy a kolekce úloh jeden premium, jste fakturovaná jednu standardní fakturace jednotku *a* jednu jednotku fakturace premium. Kusovník služby Plánovač na základě počtu aktivní úloha kolekce, které jsou nastaveny na standard nebo premium; To se vysvětluje dále v následujících dvou částech.

## <a name="standard-billable-units"></a>Standardní fakturovatelný jednotky
Standardní fakturovatelný jednotka může obsahovat až 10 kolekce standardní úloh. Vzhledem k tomu, že kolekce standardní úloh může mít až 50 úloh na kolekci úloh, jednu standardní fakturace jednotku umožňuje předplatné až 500 úlohy – až téměř 22 milionů spuštěních úloh za měsíc.

Pokud je mezi 1 a 10 kolekce standardní úloh, platit budete pro 1 jednotka standard fakturace. Pokud máte mezi kolekcí standardní úloh 11 a 20, platit budete pro 2 standardní fakturace jednotky. Pokud máte 21 až 30 standardní úlohy kolekce, platit budete pro 3 standardní fakturace jednotky a tak dále.

## <a name="p10-premium-billable-units"></a>P10 úrovně Fakturovatelný jednotky Premium
Fakturovatelný jednotka premium P10 může obsahovat až 10 000 kolekcí úloh P10 úrovně premium. Vzhledem k tomu, že kolekce úloh premium P10 může mít až 50 úloh na kolekci úloh, jednu jednotku fakturace premium umožňuje předplatné až 500 000 úlohy – až téměř 22 miliardy spuštěních úloh za měsíc.

Pokud je mezi 1 a 10 000 kolekcí úloh premium, platit budete pro 1 jednotka premium fakturace P10. Pokud máte mezi 10,001 a kolekce úloh 20 000 premium, vám budou fakturovat 2 P10 premium fakturace jednotky a tak dále.

Kolekce úloh premium P10 tedy mají stejnou funkci jako kolekce standardní úloh ale poskytují zalomení cenu v případě, že vaše aplikace vyžaduje velké množství kolekcí úloh.

## <a name="p20-premium-billable-units"></a>P20 Fakturovatelný jednotky úrovni Premium
Fakturovatelný jednotka premium P20 může obsahovat až 5000 kolekcí úloh premium P20. Vzhledem k tomu, že kolekce úloh premium P20 může mít až 1 000 úloh na kolekci úloh, jednu jednotku fakturace premium umožňuje předplatné až 5 000 000 úlohy – až téměř 220 miliardy spuštěních úloh za měsíc.

Kolekce úloh premium p20 poskytuje stejné funkce jako kolekce úloh P10 úrovně premium, ale také podporuje větší počet úloh na kolekci úloh a vyšší celkový počet úloh celkové než P10 premium umožňuje další škálovatelnost.

## <a name="billing-and-active-status"></a>Fakturace a aktivní stav
Kolekce úloh jsou vždy aktivní, pokud celé předplatné přešel do některé dočasné zakázané kvůli problémům s fakturací. Jediný způsob, jak zajistit, že není účtován kolekce úloh je buď nastavte ji na *volné* plán nebo se odstranit kolekci úloh.

I když může zakažte všechny úlohy v kolekci úloh v rámci jedné operace, nezmění fakturace stav kolekce úloh – kolekce úloh se *stále* platit. Kolekce úloh prázdný podobně, jsou považovány za aktivní a bude účtován.

## <a name="pricing"></a>Ceny
Podrobnosti o cenách najdete v tématu [ceny Plánovač](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

