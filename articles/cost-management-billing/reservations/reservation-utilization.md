---
title: Zobrazit využití rezervace Azure
description: Přečtěte si, jak získat informace o využití a podrobnostech rezervace.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568942"
---
# <a name="view-reservation-utilization-after-purchase"></a>Zobrazit využití rezervace po nákupu

Můžete zobrazit procento využití rezervace a prostředky, které tuto rezervaci použily v Azure Portal a v aplikaci Cost Management Power BI.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>Zobrazení využití v Azure Portal pomocí přístupu k Azure RBAC

Pokud chcete zobrazit využití rezervace, musíte mít přístup k rezervované službě Azure RBAC nebo musíte mít zvýšený přístup ke správě všech předplatných Azure a skupin pro správu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do části [Rezervace](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade).
1. V seznamu se zobrazí všechny rezervace, u kterých máte roli Vlastník nebo Čtenář. U každé rezervace se zobrazí poslední známé procento využití.
1. Výběrem procenta využití zobrazíte historii a podrobnosti využití. Příklad ukazuje následující video.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Zobrazit využití jako správce fakturace

Správce smlouva Enterprise (EA) nebo správce fakturace v rámci smlouvy Microsoft Customer Agreement (MCA) může zobrazit využití z **cost management a fakturace**.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejít na **cost management +**  >  **rezervace** fakturace.
1. Výběrem procenta využití zobrazíte historii a podrobnosti využití.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Získání informací o rezervacích a využití pomocí rozhraní API, PowerShellu a rozhraní příkazového řádku

[Využití rezervace](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) můžete získat pomocí rozhraní API využití rezervovaných instancí.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Zobrazení rezervací a využití v Power BI

K dispozici jsou dvě možnosti pro Power BI uživatele:

- Azure Cost Management konektor pro Power BI Desktop datum nákupu rezervace a data o využití jsou k dispozici v [konektoru Azure cost management pro Power BI Desktop](/power-bi/desktop-connect-azure-cost-management). Pomocí konektoru můžete vytvořit sestavy, které chcete použít.
- Azure Cost Management Power BI aplikaci – použijte [aplikaci Azure Cost Management Power BI](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) pro předem vytvořené sestavy, které můžete dále přizpůsobit.

## <a name="next-steps"></a>Další kroky

- [Správa rezervací Azure](manage-reserved-vm-instance.md)
- [Vysvětlení využití rezervací u předplatného s průběžnými platbami](understand-reserved-instance-usage.md)
- [Vysvětlení využití rezervací u smlouvy Enterprise](understand-reserved-instance-usage-ea.md)
- [Vysvětlení využití rezervací u předplatných CSP](/partner-center/azure-reservations)
