---
title: Nabídky spravovaných služeb na Azure Marketplace
description: Nabídky spravované služby umožňují zákazníkům v Azure Marketplace prodávat nabídky správy prostředků.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: e66a87751e0375804031e777a40c5fc1d0e8a7e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092996"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Nabídky spravovaných služeb na Azure Marketplace

Tento článek popisuje typ nabídky **spravované služby** v [Azure Marketplace](https://azuremarketplace.microsoft.com). Nabídky spravované služby umožňují zákazníkům nabízet služby správy prostředků prostřednictvím [Azure Lighthouse](../overview.md). Tyto nabídky můžete zpřístupnit všem potenciálním zákazníkům nebo pouze jednomu nebo více konkrétním zákazníkům. Vzhledem k tomu, že se zákazníci fakturují přímo na náklady související s těmito spravovanými službami, neúčtují žádné poplatky za vás Microsoft.

## <a name="understand-managed-service-offers"></a>Vysvětlení nabídek spravovaných služeb

Spravovaná služba nabízí zjednodušení procesu připojování zákazníků do Azure Lighthouse. Když zákazník koupí nabídku v Azure Marketplace, bude moct určit, které předplatné nebo skupiny prostředků by se měly připojit.

Potom budou uživatelé ve vaší organizaci moci pracovat na těchto prostředcích v rámci vašeho spravovaného klienta prostřednictvím [delegované správy prostředků Azure](azure-delegated-resource-management.md)podle přístupu, který jste definovali při vytváření nabídky. To se provádí pomocí manifestu, který určuje Azure Active Directory (Azure AD) uživatelů, skupin a instančních objektů, které budou mít přístup k zákaznickým prostředkům, spolu s [rolemi](tenants-users-roles.md) , které definují jejich úroveň přístupu.

> [!NOTE]
> Nabídky spravované služby nemusí být k dispozici v Azure Government a dalších národních cloudech.

## <a name="public-and-private-offers"></a>Veřejné a soukromé nabídky

Každá nabídka spravované služby zahrnuje jeden nebo více plánů. Plány můžou být buď soukromé, nebo veřejné.

Pokud chcete omezit nabídku na konkrétní zákazníky, můžete publikovat soukromý plán. V takovém případě se plán dá koupit jenom pro konkrétní ID předplatného, které zadáte. Další informace najdete v tématu [soukromé nabídky](../../marketplace/private-offers.md).

> [!NOTE]
> Soukromé nabídky nejsou podporovány u předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP).

Veřejné plány umožňují propagovat vaše služby na nové zákazníky. To je obvykle vhodnější, pokud požadujete pouze omezený přístup k tenantovi zákazníka. Po navázání vztahu se zákazníkem, pokud se rozhodne udělit vaší organizaci dodatečný přístup, můžete to udělat buď publikováním nového privátního plánu jenom pro tohoto zákazníka, nebo jeho [zprovozněním pro další přístup pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md).

V případě potřeby můžete do stejné nabídky zahrnout jak veřejné, tak i privátní plány.

> [!IMPORTANT]
> Jakmile je plán publikovaný jako veřejný, nemůžete ho změnit na privátní. Chcete-li určit, kteří zákazníci mohou přijmout vaši nabídku a delegovat prostředky, použijte privátní plán. S veřejným plánem nemůžete omezit dostupnost na konkrétní zákazníky nebo dokonce na určitý počet zákazníků (i když se rozhodnete, že můžete plán úplně zastavit). [Přístup k delegování můžete odebrat](../how-to/remove-delegation.md) poté, co zákazník přijme nabídku, pouze pokud jste při publikování této nabídky zazahrnuli **autorizaci** s **definicí role** nastavenou na možnost [přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) . Můžete se také obrátit na zákazníka a požádat ho, aby [Váš přístup odebral](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publikování nabídek spravovaných služeb

Informace o tom, jak publikovat nabídku spravované služby, najdete v tématu [publikování nabídky spravované služby pro Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [správě prostředků delegovaných v Azure](azure-delegated-resource-management.md) a o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- [Publikujte nabídky spravované služby](../how-to/publish-managed-services-offers.md) Azure Marketplace.
