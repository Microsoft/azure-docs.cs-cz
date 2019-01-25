---
title: Rozhraní API pro rezervaci Azure automation | Dokumentace Microsoftu
description: Další informace o rozhraní API služby Azure, můžete získat prostřednictvím kódu programu informace o rezervaci.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/10/2018
ms.author: banders
ms.openlocfilehash: a71c456f017e80da85f27437738aa963ec967a3c
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904150"
---
# <a name="apis-for-azure-reservation-automation"></a>Rozhraní API pro rezervaci Azure automation

Pomocí rozhraní API pro Azure prostřednictvím kódu programu získat informace o vaší organizaci o Azure service nebo software rezervace.

## <a name="find-reservation-plans-to-buy"></a>Najít plány rezervace zakoupit

Pomocí rezervace doporučení rozhraní API pro doporučení, na kterých chcete koupit rezervace založeno na používání ve vaší organizaci. Další informace najdete v tématu [doporučení rezervace](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Můžete také analyzovat využití prostředků pomocí podrobností využití rozhraní API využití. Další informace najdete v tématu [podrobnosti o použití – seznam pro fakturační období podle fakturačního účtu](/rest/api/consumption/usagedetails/listforbillingperiodbybillingaccount). Prostředky Azure, které používají konzistentní jsou obvykle nejlepší kandidát pro rezervaci.

## <a name="buy-a-reservation"></a>Nákup rezervace

Nelze nakupovat aktuálně rezervaci prostřednictvím kódu programu. Pokud chcete koupit rezervaci, naleznete v následujících článcích:

Plány služby:
- [Virtuální počítač](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Databáze Cosmos](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Softwarové plány:
- [Software SUSE Linuxem](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Získat rezervace

Pokud jste zákazník Azure se smlouvou Enterprise (EA zákazníků), můžete získat rezervace organizaci zakoupíte pomocí [transakce získat rezervované Instance se účtuje rozhraní API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). Pro další předplatná, získat seznam rezervací, které jste koupili a máte oprávnění k zobrazení pomocí rozhraní API [objednávka rezervace – seznam](/rest/api/reserved-vm-instances/reservationorder/list). Vlastník účtu služby nebo osoby, kterou zakoupíte rezervaci má standardně oprávnění k zobrazení rezervace.

## <a name="see-reservation-usage"></a>Zobrazit využití rezervace

Pokud jste zákazník EA, můžete programově zobrazit používání rezervace ve vaší organizaci. Další informace najdete v tématu [využití získat rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). Pro další předplatná, použijte rozhraní API [rezervace souhrny – seznam podle rezervace pořadí a rezervace](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Pokud zjistíte, že vaše organizace rezervace se podceňované:

- Zajistěte, aby virtuální počítače, které vytváří organizace na velikost virtuálního počítače, který je na rezervaci.
- Ujistěte se, že je velikost flexibilita instancí na. Další informace najdete v tématu [spravovat rezervace - změna optimalizovat nastavení Reserved VM instances](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Změňte rozsah rezervace sdílet tak, aby platil šířeji. Další informace najdete v tématu [Správa rezervací – změnit obor pro rezervaci](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).
- Výměna nevyužité množství. Další informace najdete v tématu [Správa rezervací – zrušení a výměnu](billing-manage-reserved-vm-instance.md#cancellations-and-exchanges).

## <a name="give-access-to-reservations"></a>Poskytnout přístup k rezervace

Získání seznamu všech rezervací, že uživatel má přístup k pomocí [rezervace - operace – rozhraní API seznam](/rest/api/reserved-vm-instances/reservationorder/list). Udělit přístup k rezervaci prostřednictvím kódu programu, najdete v následujících článcích:

- [Správa přístupu pomocí RBAC a rozhraní REST API](../role-based-access-control/role-assignments-rest.md)
- [Správa přístupu pomocí RBAC a prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Správa přístupu pomocí RBAC a rozhraní příkazového řádku Azure](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Dělené tunelové propojení nebo sloučení rezervace

Po nákupu více než jednu instanci prostředků v rámci rezervace, můžete přiřadit instancí v rámci této rezervaci do různých předplatných. Obor rezervace můžete změnit tak, aby se vztahuje na všechna předplatná v rámci stejného kontextu fakturace. Ale pro účely náklady na správu nebo plánování rozpočtu, můžete chtít zachovat oboru jako "jedno předplatné" a přiřaďte rezervované instance pro konkrétní předplatné. 

Pokud chcete rozdělit rezervaci, použijte rozhraní API [rezervace - rozdělit](/rest/api/reserved-vm-instances/reservation/split). Rezervaci můžete rozdělit pomocí prostředí PowerShell. Další informace najdete v tématu [Správa rezervací - rezervace rozdělit do dvou rezervace](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Chcete-li sloučit dvě rezervace do jednu rezervaci, použijte rozhraní API [rezervace - Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Změnit obor pro rezervaci.

Rozsah rezervace může být jedno předplatné nebo všechna předplatná v fakturační kontextu. Je-li nastavit obor na jedno předplatné, rezervace porovnáváno s prostředky ve vybraném předplatném. Je-li nastavit obor na sdílené Azure odpovídá rezervace k prostředkům, které běží ve všech předplatných v rámci kontextu fakturace. Kontext fakturace je závislá na předplatné, které jste použili k koupit rezervaci. Další informace najdete v tématu [Správa rezervací – Změna rozsahu](billing-manage-reserved-vm-instance.md#change-the-scope-for-a-reservation).

Chcete-li změnit obor prostřednictvím kódu programu, použijte rozhraní API [rezervace - aktualizace](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Další informace

- [Co jsou rezervace pro Azure](billing-save-compute-costs-reservations.md)
- [Vysvětlení, používání sleva za rezervaci virtuálních počítačů](billing-understand-vm-reservation-charges.md)
- [Vysvětlení, jak se použije slevu plán softwaru SUSE Linux Enterprise](billing-understand-suse-reservation-charges.md)
- [Vysvětlení, jak se používají jiné slevy na rezervaci](billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervace pro vaše předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervaci u prováděcí smlouvy Enterprise](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software Windows, které nejsou součástí rezervace](billing-reserved-instance-windows-software-costs.md)
- [Rezervace Azure v programu Partnerské centrum Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)