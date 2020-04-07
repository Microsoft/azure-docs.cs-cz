---
title: Nabídky spravovaných služeb na Azure Marketplace
description: Nabídky spravovaných služeb umožňují poskytovatelům služeb prodávat nabídky správy prostředků zákazníkům na Azure Marketplace.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672426"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Nabídky spravovaných služeb na Azure Marketplace

Tento článek popisuje typ nabídky **spravované služby** na [Azure Marketplace](https://azuremarketplace.microsoft.com). Nabídky spravovaných služeb umožňují nabízet služby správy prostředků zákazníkům pomocí [azure delegované správy prostředků](azure-delegated-resource-management.md). Tyto nabídky můžete zpřístupnit všem potenciálním zákazníkům nebo pouze jednomu nebo více konkrétním zákazníkům. Vzhledem k tomu, že zákazníkům přímo účtujete náklady související s těmito spravovanými službami, společnost Microsoft neúčtuje žádné poplatky.

## <a name="understand-managed-service-offers"></a>Principy nabídek spravovaných služeb

Spravovaná služba nabízí zjednodušit proces registrace zákazníků pro správu delegovaných prostředků Azure. Když si zákazník zakoupí nabídku na Azure Marketplace, bude moct určit, která předplatná nebo skupiny prostředků by měly být na palubě.

Poté budou uživatelé ve vaší organizaci moci pracovat na těchto prostředcích z tenanta vaší organizace podle přístupu, který jste definovali při vytváření nabídky na [portálu partnerů cloudu](https://cloudpartner.azure.com/). To se provádí prostřednictvím manifestu, který určuje uživatele, skupiny a instanční objekty Azure AD, které budou mít přístup k prostředkům zákazníků pomocí správy delegovaných prostředků Azure, spolu s rolemi, které definují jejich úroveň přístupu. Přiřazením oprávnění skupině Azure AD, nikoli řadě účtů jednotlivých uživatelů nebo aplikací, můžete přidat nebo odebrat jednotlivé uživatele, když se změní vaše požadavky na přístup.

## <a name="public-and-private-offers"></a>Veřejné a soukromé nabídky

Každá nabídka spravovaných služeb zahrnuje jeden nebo více plánů. Plány mohou být soukromé nebo veřejné.

Pokud chcete nabídku omezit na konkrétní zákazníky, můžete publikovat soukromý plán. Pokud tak učiníte, plán lze zakoupit pouze pro konkrétní] ID předplatného, které zadáte. Další informace najdete [v tématu Soukromé nabídky](../../marketplace/private-offers.md).

Veřejné plány umožňují propagovat vaše služby novým zákazníkům. Ty jsou obvykle vhodnější, pokud vyžadujete pouze omezený přístup k tenantovi zákazníka. Jakmile nastoupíte vztah se zákazníkem, pokud se rozhodne udělit vaší organizaci další přístup, můžete tak učinit buď publikováním nového soukromého plánu pouze pro tohoto zákazníka, nebo jeho [připojením k dalšímu přístupu pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md).

V případě potřeby můžete do stejné nabídky zahrnout veřejné i soukromé plány.

> [!IMPORTANT]
> Jakmile je plán publikován jako veřejný, nelze jej změnit na soukromý. Chcete-li určit, kteří zákazníci mohou vaši nabídku přijmout a delegovat prostředky, použijte soukromý plán. S veřejným tarifem nemůžete omezit dostupnost na určité zákazníky nebo dokonce na určitý počet zákazníků (i když můžete plán zcela ukončit, pokud se tak rozhodnete). Přístup [k delegování](../how-to/onboard-customer.md#remove-access-to-a-delegation) můžete odebrat poté, co zákazník přijme nabídku, pouze pokud jste při publikování nabídky zahrnuli **autorizaci** s **definicí role** nastavenou na [přiřazení registrace spravovaných služeb delete.](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) Můžete také kontaktovat zákazníka a požádat ho o [odebrání přístupu](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publikování nabídek spravovaných služeb

Informace o tom, jak publikovat nabídku spravovaných služeb, najdete v [tématu Publikování nabídky spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md). Obecné informace o publikování na Azure Marketplace pomocí portálu cloudových partnerů najdete v [tématu Azure Marketplace a Průvodce publikováním AppSource](../../marketplace/marketplace-publishers-guide.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [azure delegované správě prostředků](azure-delegated-resource-management.md) a [prostředí pro správu mezi tenanty](cross-tenant-management-experience.md).
- [Publikujte nabídky spravovaných služeb](../how-to/publish-managed-services-offers.md) na Azure Marketplace.
