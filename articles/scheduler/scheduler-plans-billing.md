---
title: Plány a fakturace ve službě Azure Scheduler
description: Plány a fakturace ve službě Azure Scheduler
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
ms.openlocfilehash: 03f335634b7ce1fe4aa6251d6ec21922ed9b84c8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887483"
---
# <a name="plans-and-billing-in-azure-scheduler"></a>Plány a fakturace ve službě Azure Scheduler
## <a name="job-collection-plans"></a>Plány úlohy shromažďování dat
Kolekce úloh jsou fakturovatelné entity ve službě Azure Scheduler. Kolekce úloh obsahují počet úloh a se dělí na tři plány – Standard, P10 Premium a P20 Premium –, které jsou popsány níže.

| **Plán kolekce úloh** | **Maximální počet úloh na kolekci úloh** | **Maximální počet opakování** | **Kolekce maximální počet úloh podle předplatného** | **Omezení** |
|:--- |:--- |:--- |:--- |:--- |
| **Standard** |50 úloh na kolekci úloh |Jednou za minutu. Nelze spustit úlohy častěji než jednou za minutu |Předplatné je povolená až 100 kolekcí standardní úlohy |Přístup k úplná sada funkcí služby Scheduler |
| **P10 Premium** |50 úloh na kolekci úloh |Jednou za minutu. Nelze spustit úlohy častěji než jednou za minutu |Předplatné je povolená až 10 000 kolekce úloh P10 Premium. <a href="mailto:wapteams@microsoft.com">Kontaktujte nás</a> Další informace. |Přístup k úplná sada funkcí služby Scheduler |
| **P20 Premium** |1 000 úloh na kolekci úloh |Jednou za minutu. Nelze spustit úlohy častěji než jednou za minutu |Předplatné je povolená až 10 000 kolekcí úloh P20 Premium. <a href="mailto:wapteams@microsoft.com">Kontaktujte nás</a> Další informace. |Přístup k úplná sada funkcí služby Scheduler |

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Upgrady a Downgrady plány úlohy shromažďování dat
Můžete upgradovat nebo downgradovat plán kolekce úloh kdykoli mezi plány Standard, P10 Premium a P20 Premium.

## <a name="billing-and-azure-plans"></a>Plány a fakturace Azure
Pokud máte více než 100 kolekcí úloh standard (10 standardních jednotek fakturace), je lepší nabídku pro všechny kolekce úloh v plánu premium.

Pokud máte jednu kolekci úloh standard a premium jedna kolekce úloh se fakturuje jeden fakturační jednotka na úrovni standard *a* jeden fakturační jednotka na úrovni premium. Faktury služby Plánovač na základě počtu aktivní kolekce úloh, které jsou nastavené na úroveň standard nebo premium; To je vysvětleno dále v následujících dvou částech.

## <a name="standard-billable-units"></a>Fakturovatelné jednotky na úrovni Standard
Standardní fakturovatelná jednotka může obsahovat až 10 kolekcí standardní úloh. Protože kolekce standardní úloh může mít až 50 úloh na kolekci úloh, umožňuje jedna jednotka na úrovni standard fakturační předplatnému až 500 úlohy – až téměř 22 milionů provedení úloh za měsíc.

Pokud budete mít mezi 1 a 10 kolekcí úloh standardní, vám budeme účtovat jedna jednotka na úrovni standard fakturace. Pokud máte mezi 11 a kolekcí úloh 20 standard vám budeme účtovat dvě fakturační jednotky na úrovni standard. Pokud máte mezi 21 a kolekcí úloh 30 standardní, bude se účtovat tři standardních jednotek fakturace a tak dále.

## <a name="p10-premium-billable-units"></a>P10 Fakturovatelné jednotky na úrovni Premium
Fakturovatelná jednotka úrovně premium P10 může obsahovat až 10 000 kolekce úloh P10 premium. Protože kolekce úloh P10 premium může mít až 50 úloh na kolekci úloh, umožňuje jedna jednotka na úrovni premium fakturační předplatnému až pro 500 000 úlohy – až téměř 22 miliard provedení úloh za měsíc.

Pokud budete mít mezi 1 a 10 000 kolekcí úloh premium, vám budeme účtovat jedna P10 fakturační jednotka na úrovni premium. Pokud máte mezi 10,001 a kolekcí úloh 20 000 premium, bude se účtovat 2 jednotky fakturační úrovně premium P10 a tak dále.

Kolekce úloh P10 premium proto mají stejné funkce jako standardní úlohy kolekce ale poskytnout zalomení cena v případě, že vaše aplikace vyžaduje mnoha kolekcí úloh.

## <a name="p20-premium-billable-units"></a>P20 Fakturovatelné jednotky na úrovni Premium
Fakturovatelná jednotka úrovně premium P20 může obsahovat až 5 000 kolekcí úloh P20 premium. Protože kolekce úloh P20 premium může mít až 1 000 úloh na kolekci úloh, umožňuje jedna jednotka na úrovni premium fakturační předplatnému až 5 000 000 úlohy – až téměř 220 miliard provedení úloh za měsíc.

Kolekce úloh p20 premium poskytují stejné funkce jako kolekce úloh P10 premium, ale také podporuje větší počet úloh na kolekci úloh a větší celkový počet úloh celkové než P10 premium umožňuje další škálovatelnost.

## <a name="billing-and-active-status"></a>Fakturace a aktivní stav
Kolekce úloh jsou vždy aktivní, pokud celé předplatné přešel do některé dočasné zakázaná kvůli problémům s fakturací. Jediný způsob, jak zajistit, že se neúčtuje kolekce úloh je odstranit kolekci úloh.

I když můžete kdykoli deaktivovat všechny úlohy v kolekci úloh v rámci jedné operace, nezmění stav fakturace kolekce úloh – kolekce úloh se *stále* účtovat. Kolekce úloh prázdný podobně, jsou považovány za aktivní a budou se fakturovat.

## <a name="pricing"></a>Ceny
Podrobnosti o cenách najdete v tématu [cenách Scheduleru](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

