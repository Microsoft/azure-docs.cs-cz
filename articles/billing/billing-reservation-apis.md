---
title: Rozhraní API pro automatizaci rezervací Azure | Microsoft Docs
description: Přečtěte si o rozhraních API Azure, pomocí kterých můžete programově získat informace o rezervacích.
author: yashesvi
manager: yashesvi
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2019
ms.author: banders
ms.openlocfilehash: 36bc403c4000e58541f22c2cb44f77a28e81cb72
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779894"
---
# <a name="apis-for-azure-reservation-automation"></a>Rozhraní API pro automatizaci rezervací Azure

Pomocí rozhraní API Azure můžete programově získat informace pro vaši organizaci o rezervacích služby a softwaru Azure.

## <a name="find-reservation-plans-to-buy"></a>Hledání plánů rezervací k nákupu

Pomocí rozhraní API pro doporučení rezervací získáte doporučení, na jejichž základě se plán rezervací má koupit na základě využití vaší organizace. Další informace najdete v tématu [získání doporučení pro rezervaci](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).

Využití prostředků můžete analyzovat také pomocí podrobností využití rozhraní API spotřeby. Další informace najdete v tématu [Podrobnosti o využití – seznam pro fakturační období podle fakturačního účtu](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslistforbillingperiod). Prostředky Azure, které používáte konzistentně, jsou většinou nejlepším kandidátem na rezervaci.

## <a name="buy-a-reservation"></a>Nákup rezervace

Rezervace a softwarové plány Azure můžete koupit programově pomocí rozhraní REST API. Další informace najdete v tématu věnovaném [rozhraní API pro nákup rezervací](/rest/api/reserved-vm-instances/reservationorder/purchase).

Tady je ukázkový požadavek k nákupu pomocí REST API:

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

Rezervaci můžete také zakoupit v Azure Portal. Další informace najdete v následujících článcích:

Plány služeb:
- [Virtuální počítač](../virtual-machines/windows/prepay-reserved-vm-instances.md?toc=/azure/billing/TOC.json)
-  [Databáze Cosmos](../cosmos-db/cosmos-db-reserved-capacity.md?toc=/azure/billing/TOC.json)
- [SQL Database](../sql-database/sql-database-reserved-capacity.md?toc=/azure/billing/TOC.json)

Softwarové plány:
- [Software SUSE Linux](../virtual-machines/linux/prepay-suse-software-charges.md?toc=/azure/billing/TOC.json)

## <a name="get-reservations"></a>Získat rezervace

Pokud jste zákazníkem Azure s smlouva Enterprise (EA), můžete získat rezervace, které vaše organizace nakoupila, pomocí [rozhraní API pro získání poplatků za transakce s využíváním rezervované instance](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges). V případě jiných předplatných Získejte seznam rezervací, které jste nakoupili, a máte oprávnění k zobrazení pomocí [seznamu pořadí rezervací](/rest/api/reserved-vm-instances/reservationorder/list)rozhraní API. Ve výchozím nastavení má vlastník účtu nebo osoba, která rezervaci nakoupila, oprávnění k zobrazení rezervace.

## <a name="see-reservation-usage"></a>Viz použití rezervací

Pokud jste zákazníkem se smlouvou EA, můžete programově zobrazit způsob použití rezervací ve vaší organizaci. Další informace najdete v tématu [získání využití rezervovaných instancí pro podnikové zákazníky](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage). U ostatních předplatných použijte [Souhrn rezervací rozhraní API – seznam podle pořadí rezervací a rezervací](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

Pokud zjistíte, že se používají rezervace vaší organizace:

- Zajistěte, aby se virtuální počítače, které vaše organizace vytváří, shodovaly s velikostí virtuálního počítače, který je na rezervaci.
- Ujistěte se, že je zapnutá flexibilita velikosti instance. Další informace najdete v tématu [Správa rezervací – Změna nastavení optimalizace pro rezervované instance virtuálních počítačů](billing-manage-reserved-vm-instance.md#change-optimize-setting-for-reserved-vm-instances).
- Změňte rozsah rezervace na Shared tak, aby se v podstatě platilo. Další informace najdete v tématu [Správa rezervací – Změna rozsahu rezervace](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).
- Výměna nevyužitého množství. Další informace najdete v tématu [Správa rezervací](billing-manage-reserved-vm-instance.md).

## <a name="give-access-to-reservations"></a>Udělit přístup k rezervacím

Získá seznam všech rezervací, ke kterým má uživatel přístup, pomocí rozhraní API pro vydanou [rezervaci a Operations-list](/rest/api/reserved-vm-instances/reservationorder/list). Pokud chcete přístup k rezervovanému programu udělit programově, přečtěte si jeden z následujících článků:

- [Správa přístupu pomocí RBAC a REST API](../role-based-access-control/role-assignments-rest.md)
- [Správa přístupu pomocí RBAC a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Správa přístupu pomocí RBAC a Azure CLI](../role-based-access-control/role-assignments-cli.md)

## <a name="split-or-merge-reservation"></a>Rozdělit nebo sloučit rezervaci

Po zakoupení více než jedné instance prostředku v rámci rezervace můžete chtít přiřadit instance v rámci této rezervace různým předplatným. Rozsah rezervace můžete změnit tak, aby platil pro všechna předplatná v rámci stejného fakturačního kontextu. Pro účely správy nákladů nebo rozpočtování ale můžete chtít ponechat obor jako "jednotné předplatné" a přiřadit instance rezervací ke konkrétnímu předplatnému.

Pokud chcete rozdělit rezervaci, použijte rezervaci rozhraní API [– rozdělení](/rest/api/reserved-vm-instances/reservation/split). Rezervaci můžete také rozdělit pomocí prostředí PowerShell. Další informace najdete v tématu [Správa rezervací – rozdělení rezervací do dvou rezervací](billing-manage-reserved-vm-instance.md#split-a-single-reservation-into-two-reservations).

K sloučení dvou rezervací do jedné rezervace použijte rezervaci rozhraní API [– Sloučit](/rest/api/reserved-vm-instances/reservation/merge).

## <a name="change-scope-for-a-reservation"></a>Změna rozsahu rezervace

Rozsahem rezervace může být jedno předplatné, jedna skupina prostředků nebo všechny odběry v rámci vašeho fakturačního kontextu. Pokud nastavíte obor na jedno předplatné nebo jednu skupinu prostředků, rezervace se bude shodovat se spuštěnými prostředky ve vybraném předplatném. Pokud odstraníte nebo přesunete předplatné nebo skupinu prostředků, rezervace se nevyužije.  Pokud nastavíte obor na Shared, Azure bude odpovídat rezervacím na prostředky, které běží ve všech předplatných v rámci fakturačního kontextu. Fakturační kontext je závislý na předplatném, které jste použili k nákupu rezervace. Můžete vybrat rozsah při nákupu nebo ho kdykoli změnit po nákupu. Další informace najdete v tématu [Správa rezervací – Změna oboru](billing-manage-reserved-vm-instance.md#change-the-reservation-scope).

Pokud chcete obor změnit programově, použijte rezervaci rozhraní API [– aktualizace](/rest/api/reserved-vm-instances/reservation/update).

## <a name="learn-more"></a>Víc se uč

- [Co jsou rezervace pro Azure](billing-save-compute-costs-reservations.md)
- [Vysvětlení způsobu použití slevy rezervace virtuálních počítačů](billing-understand-vm-reservation-charges.md)
- [Princip použití zlevněného plánu Enterprise software SUSE Linux](billing-understand-suse-reservation-charges.md)
- [Informace o tom, jak se používají jiné slevy rezervace](billing-understand-reservation-charges.md)
- [Vysvětlení využití rezervací pro předplatné s průběžnými platbami](billing-understand-reserved-instance-usage.md)
- [Vysvětlení použití rezervací pro podnikovou registraci](billing-understand-reserved-instance-usage-ea.md)
- [Náklady na software systému Windows, které nejsou součástí rezervací](billing-reserved-instance-windows-software-costs.md)
- [Azure Reservations v programu partner Center pro Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-reservations)
