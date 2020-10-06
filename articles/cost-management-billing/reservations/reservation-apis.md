---
title: Rozhraní API pro automatizaci rezervací Azure | Microsoft Docs
description: Přečtěte si o rozhraních API pro Azure, pomocí kterých můžete informace o rezervacích získávat prostřednictvím programu.
author: yashesvi
ms.reviewer: yashesvi
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 12b20f7556a460506067305401fb2379678ee58e
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91371690"
---
# <a name="apis-for-azure-reservation-automation"></a>Rozhraní API pro automatizaci rezervací Azure

Pomocí rozhraní API pro Azure můžete informace o rezervacích služeb nebo softwaru Azure ve vaší organizaci získávat prostřednictvím programu.

## <a name="find-reservation-plans-to-buy"></a>Vyhledání plánů rezervací k zakoupení

Na základě informací o využití ve vaší organizaci můžete pomocí rozhraní API pro doporučení rezervací získávat doporučení plánu rezervací, který byste měli zakoupit. Další informace najdete v článku o [získání doporučení rezervací](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Využití prostředků můžete analyzovat také pomocí údajů o využití rozhraní API služby Consumption. Další informace najdete v [údajích o využití ve výpisu pro fakturační období podle fakturačního účtu](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod-legacy). Nejvhodnějším kandidátem pro rezervaci jsou obvykle prostředky Azure, které používáte konzistentně.

## <a name="buy-a-reservation"></a>Nákup rezervace

Softwarové plány a rezervace Azure můžete zakoupit prostřednictvím programu pomocí rozhraní REST API. Další informace najdete v článku o rozhraní [Reservation Order – Purchase API](/rest/api/reserved-vm-instances/reservationorder/purchase).

Tady je ukázkový požadavek pro nákup prostřednictvím rozhraní REST API:

```
PUT https://management.azure.com/providers/Microsoft.Capacity/reservationOrders/<GUID>?api-version=2019-04-01
```

Text požadavku:

```
{
 "sku": {
    "name": "standard_D1"
  },
 "location": "westus",
 "properties": {
    "reservedResourceType": "VirtualMachines",
    "billingScopeId": "/subscriptions/ed3a1871-612d-abcd-a849-c2542a68be83",
    "term": "P1Y",
    "quantity": "1",
    "displayName": "TestReservationOrder",
    "appliedScopes": null,
    "appliedScopeType": "Shared",
    "reservedResourceProperties": {
      "instanceFlexibility": "On"
    }
  }
}
```

Rezervaci můžete zakoupit také na webu Azure Portal. Další informace najdete v následujících článcích:

Plány služby:
- [Virtuální počítač](../../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Databáze Cosmos](../../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md?toc=/azure/billing/TOC.json)

Softwarové plány:
- [Software SUSE Linux](../../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Získání rezervací

Pokud jste zákazníkem Azure se smlouvou Enterprise (zákazníkem EA), můžete získat rezervace, které vaše organizace zakoupila, pomocí rozhraní [Get Reserved Instance transaction charges API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). U jiných předplatných získáte seznam rezervací, které jste zakoupili a ke kterým máte oprávnění k zobrazení, pomocí rozhraní API [Reservation Order – List](/rest/api/reserved-vm-instances/reservationorder/list). Ve výchozím nastavení má vlastník účtu nebo osoba, která rezervaci zakoupila, oprávnění k zobrazení rezervace.

## <a name="see-reservation-usage"></a>Zobrazení využití rezervací

Pokud jste zákazníkem EA, můžete využití rezervací ve vaší organizaci zobrazit prostřednictvím programu. Další informace najdete v článku o [zjištění využití rezervovaných instancí u podnikových zákazníků](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). U jiných předplatných použijte rozhraní API [Reservations Summaries – List By Reservation Order And Reservation](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Pokud zjistíte, že se rezervace vaší organizace využívají málo:

- Ujistěte se, že virtuální počítače, které vaše organizace vytváří, odpovídají velikosti virtuálních počítačů v rezervaci.
- Ujistěte se, že je povolená flexibilita velikosti instance. Další informace najdete v článku [Správa rezervací – změna nastavení optimalizace u rezervovaných instancí virtuálních počítačů](manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Změňte rozsah rezervace na sdílený, aby se rezervace uplatnila ve větší šíři. Další informace najdete v článku [Správa rezervací – změna rozsahu rezervace](manage-reserved-vm-instance.md#change-the-reservation-scope).
- Proveďte výměnu nevyužitého množství. Další informace najdete v článku [Správa rezervací](manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Udělení přístupu k rezervacím

Seznam všech rezervací, ke kterým má uživatel přístup, lze získat pomocí rozhraní [Reservation – Operation – List API](/rest/api/reserved-vm-instances/reservationorder/list). Pokud chcete udělit přístup k rezervaci prostřednictvím programu, přečtěte si následující články:

- [Přidání nebo odebrání přiřazení rolí v Azure pomocí REST API](../../role-based-access-control/role-assignments-rest.md)
- [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure PowerShellu](../../role-based-access-control/role-assignments-powershell.md)
- [Přidání nebo odebrání přiřazení rolí v Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Rozdělení nebo sloučení rezervace

Po zakoupení více než jedné instance prostředku v rámci rezervace můžete instance v této rezervaci přiřadit různým předplatným. Rozsah rezervace můžete změnit tak, aby se vztahoval na všechna předplatná v rámci stejného kontextu fakturace. Pro účely správy nákladů nebo plánování rozpočtu ale můžete rozsah ponechat jako „jedno předplatné“ a instance v rezervaci přiřadit k určitému předplatnému.

K rozdělení rezervace použijte rozhraní API [Reservation – Split](/rest/api/reserved-vm-instances/reservation/split). Rezervaci můžete rozdělit také pomocí PowerShellu. Další informace najdete v článku [Správa rezervací – rozdělení rezervace na dvě rezervace](manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

Ke sloučení dvou rezervací do jedné rezervace použijte rozhraní API [Reservation – Merge](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Změna rozsahu rezervace

Rozsahem rezervace může být jedno předplatné, jedna skupina prostředků nebo všechna předplatná ve vašem kontextu fakturace. Pokud nastavíte rozsah na jedno předplatné nebo jednu skupinu prostředků, rezervace bude odpovídat používaným prostředkům ve vybraném předplatném. Pokud příslušné předplatné nebo skupinu prostředků odstraníte nebo přesunete, rezervace se nevyužije.  Pokud nastavíte sdílený rozsah, Azure přiřadí rezervaci k prostředkům používaným ve všech předplatných v daném kontextu fakturace. Kontext fakturace závisí na předplatném, které jste použili k zakoupení rezervace. Rozsah můžete vybrat při nákupu a lze ho kdykoli po nákupu změnit. Další informace najdete v článku [Správa rezervací – změna rozsahu](manage-reserved-vm-instance.md#change-the-reservation-scope).

Ke změně rozsahu prostřednictvím programu použijte rozhraní API [Reservation – Update](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Další informace

- [Co jsou rezervace v Azure](save-compute-costs-reservations.md)
- [Vysvětlení způsobu uplatnění slevy za rezervaci virtuálních počítačů](../manage/understand-vm-reservation-charges.md)
- [Vysvětlení způsobu uplatnění slevy pro plán softwaru SUSE Linux Enterprise](understand-suse-reservation-charges.md)
- [Vysvětlení způsobu uplatnění slev za jiné rezervace](understand-reservation-charges.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Náklady na software pro Windows nezahrnuté v rezervacích](reserved-instance-windows-software-costs.md)
- [Rezervace Azure v programu Partner Center Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
