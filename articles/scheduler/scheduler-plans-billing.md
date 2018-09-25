---
title: Plány a fakturace – Azure Scheduleru
description: Další informace o plány a fakturace Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 13a2be8c-dc14-46cc-ab7d-5075bfd4d724
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 74f13628b62d53a84b4d19255a30a6bc4a7367ec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974285"
---
# <a name="plans-and-billing-for-azure-scheduler"></a>Plány a fakturace Azure Scheduler

> [!IMPORTANT]
> [Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduleru, která se vyřazuje. K plánování úloh, [místo toho vyzkoušet Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="job-collection-plans"></a>Plány úlohy shromažďování dat

Kolekce úloh v Azure Scheduleru, obsahuje určitý počet úloh. Kolekce úloh je fakturovatelná entita a obsahuje v plánech Standard, P10 Premium a P20 Premium, které jsou popsány zde: 

| Plán kolekce úloh | Maximální počet úloh na kolekci | Maximální opakování | Maximální počet kolekcí úloh na předplatné | Omezení | 
|:--- |:--- |:--- |:--- |:--- |
| **Standard** | 50 úloh na kolekci | Jedna za minutu. Úlohy nelze spouštět častěji než jedna za minutu. | Každé předplatné Azure může mít až 100 kolekcí standardní úloh. | Přístup k Scheduleru úplná sada funkcí | 
| **P10 Premium** | 50 úloh na kolekci | Jedna za minutu. Úlohy nelze spouštět častěji než jedna za minutu. | Každé předplatné Azure může mít až 10 000 kolekce úloh P10 Premium. Pro další kolekce <a href="mailto:wapteams@microsoft.com">kontaktujte nás</a>. | Přístup k Scheduleru úplná sada funkcí |
| **P20 Premium** | 1 000 úloh na kolekci | Jedna za minutu. Úlohy nelze spouštět častěji než jedna za minutu. | Každé předplatné Azure může mít až 5 000 kolekcí úloh P20 Premium. Pro další kolekce <a href="mailto:wapteams@microsoft.com">kontaktujte nás</a>. | Přístup k Scheduleru úplná sada funkcí |
|||||| 

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [cenách Scheduleru](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="upgrade-or-downgrade-plans"></a>Upgradovat nebo downgradovat plány

Kdykoli můžete upgradovat nebo downgradovat plán kolekce úloh v plánech Standard, P10 Premium a P20 Premium.

## <a name="active-status-and-billing"></a>Aktivní stav a fakturace

Kolekce úloh jsou vždy aktivní, pokud předplatné Azure celý přejde do o dočasný stav Zakázáno z důvodu problémů s fakturací. A přestože všech úloh v kolekci úloh prostřednictvím jediné operace můžete zakázat, tato akce nezmění stav kolekce úloh fakturace, tak kolekce úloh je *stále* účtuje. Kolekce úloh prázdný jsou považovány za aktivní a služby.

Pokud chcete mít jistotu, že kolekce úloh se neúčtuje, je nutné odstranit kolekci úloh.

## <a name="standard-billable-units"></a>Fakturovatelné jednotky na úrovni Standard

Standardní fakturovatelná jednotka může mít až 10 kolekcí standardní úloh. Protože kolekce standardní úloh může mít až 50 úloh na kolekci, jedna jednotka na úrovni standard fakturační umožňuje vašemu předplatnému Azure může mít až do 500 úloh nebo až 22 téměř *milionů* úlohy spuštění za měsíc. Tento seznam vysvětluje, jak bude se vám účtovat na základě různých čísel standardní úlohy kolekce:

* Pokud budete mít mezi 1 a 10 standardní úlohy kolekce, bude se vám účtovat pro jeden fakturační jednotka na úrovni standard. 

* Pokud máte mezi 11 a kolekcí úloh 20 Standard vám budeme fakturovat dvě fakturační jednotky na úrovni standard. 

* Pokud máte mezi 21 a kolekcí úloh 30 standardní, účtuje se za tři jednotky standardní fakturace a tak dále.

## <a name="p10-premium-billable-units"></a>Fakturovatelné jednotky na úrovni premium P10

Fakturovatelná jednotka P10 premium může mít až 10 000 kolekce úloh P10 Premium. Protože kolekce úloh P10 Premium může mít až 50 úloh na kolekci, jeden fakturační jednotka úrovně premium P10 umožní vašemu předplatnému Azure může mít až 500 000 úloh, nebo až 22 téměř *miliard* úlohy spuštění za měsíc. 

Kolekce úloh P10 Premium poskytují stejné funkce jako standardní úlohy kolekce, ale nabízejí zalomení ceny pro aplikace, které vyžadují mnoho kolekcí úloh a poskytují další škálovatelnost. Tento seznam vysvětluje, jak bude se vám účtovat na základě různých čísel kolekce úloh P10 Premium:

* Pokud budete mít mezi 1 a 10 000 kolekce úloh P10 Premium, bude se vám účtovat za jednu jednotku fakturační P10 premium. 

* Pokud máte mezi kolekce úloh P10 Premium 10,001 a 20 000, účtuje se za 2 jednotky fakturační úrovně premium P10 a tak dále.

## <a name="p20-premium-billable-units"></a>Fakturovatelné jednotky na úrovni premium p20

Fakturovatelná jednotka P20 premium může mít až 5 000 kolekcí úloh P20 Premium. Protože kolekce úloh P20 Premium může mít až 1 000 úloh na kolekci úloh, jeden fakturační jednotka úrovně premium P20 umožní vašemu předplatnému Azure může mít až 5 000 000 úloh, nebo až téměř 220 *miliard* úlohy spuštění za měsíc.

Kolekce úloh p20 Premium poskytují stejné funkce jako kolekce úloh P10 Premium, ale také kvůli podpoře větší počet úloh na kolekci a větší celkový počet úloh celkové od úrovně Premium P10, poskytuje další škálovatelnost.

## <a name="plan-comparison"></a>Porovnání plánů

* Pokud máte více než 100 kolekcí úloh Standard (10 standardních jednotek fakturace), můžete získat lepší nabídku tak, že všechny kolekce úloh v plánu Premium.

* Pokud máte jednu kolekci standardní úlohy a jedna kolekce úloh Premium, pak vám budeme fakturovat jedna jednotka na úrovni standard fakturační *a* jeden fakturační jednotka na úrovni premium.

  Služba Scheduler účtuje na základě počtu aktivní kolekce úloh, které jsou standard nebo premium.

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)