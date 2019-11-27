---
title: Nabídky spravovaných služeb na Azure Marketplace
description: Spravované služby umožňují poskytovatelům služeb prodávat nabídky správy prostředků zákazníkům v Azure Marketplace.
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: f7dcaa34b1fb471823acd74a7d9b05e42dd44161
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464000"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Nabídky spravovaných služeb na Azure Marketplace

Tento článek popisuje nový typ nabídky **spravované služby** v [Azure Marketplace](https://azuremarketplace.microsoft.com). Služby spravovaných služeb vám umožní nabízet služby správy prostředků zákazníkům se správou delegovaných prostředků Azure. Tyto nabídky můžete zpřístupnit všem potenciálním zákazníkům nebo jenom jednomu nebo více konkrétním zákazníkům. Vzhledem k tomu, že se zákazníci fakturují přímo na náklady související s těmito spravovanými službami, neúčtují žádné poplatky za vás Microsoft.

## <a name="understand-managed-services-offers"></a>Vysvětlení nabídek spravovaných služeb

Spravované služby nabízí zjednodušení procesu připojování zákazníků pro správu delegovaných prostředků Azure. Když zákazník koupí nabídku v Azure Marketplace, bude moct určit, které předplatné nebo skupiny prostředků by se měly připojit. Všimněte si, že odběr musí být nejdřív autorizovaný pro registraci, a to ruční registrací poskytovatele prostředků **Microsoft. ManagedServices** .

Potom budou uživatelé ve vaší organizaci moci provádět úlohy správy pro tyto prostředky z tenanta vaší organizace podle přístupu, který jste definovali při vytváření nabídky v [portál partnerů cloudu](https://cloudpartner.azure.com/). To se provádí pomocí manifestu, který určuje uživatele, skupiny a instanční objekty služby Azure AD, které budou mít přístup k zákaznickým prostředkům pomocí delegované správy prostředků Azure, spolu s rolemi, které definují jejich úroveň přístupu. Když pro skupinu Azure AD přiřadíte oprávnění místo řady individuálních účtů uživatelů nebo aplikací, můžete přidat nebo odebrat jednotlivé uživatele, když se vaše požadavky na přístup změní.

## <a name="public-and-private-offers"></a>Veřejné a soukromé nabídky

Každá nabídka spravovaných služeb obsahuje jeden nebo více plánů. Tyto plány můžou být buď soukromé, nebo veřejné.

Pokud chcete omezit nabídku na konkrétní zákazníky, můžete publikovat soukromý plán. V takovém případě lze plán zakoupit pouze pro konkrétní ID předplatného, které zadáte. Další informace najdete v tématu [soukromé nabídky](https://docs.microsoft.com/azure/marketplace/private-offers).

Veřejné plány umožňují propagovat vaše služby na nové zákazníky. To je obvykle vhodnější, pokud požadujete pouze omezený přístup k tenantovi zákazníka. Po navázání vztahu se zákazníkem, pokud se rozhodne udělit vaší organizaci dodatečný přístup, můžete to udělat buď publikováním nového privátního plánu jenom pro tohoto zákazníka, nebo jeho [zprovozněním pro další přístup pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md).

Mějte na paměti, že jakmile je plán publikovaný jako veřejný, nemůžete ho změnit na privátní. Navíc nemůžete omezit dostupnost veřejného plánu na konkrétní zákazníky ani na určitý počet zákazníků, i když se rozhodnete, že plán zcela zastavíte.

V případě potřeby můžete do stejné nabídky zahrnout jak veřejné, tak i privátní plány.

## <a name="publish-managed-service-offers"></a>Publikování nabídek spravovaných služeb

Informace o tom, jak publikovat nabídku spravovaných služeb, najdete v tématu [publikování nabídky spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md). Obecné informace o publikování Azure Marketplace pomocí portál partnerů cloudu najdete v tématu Příručka pro [publikování Azure Marketplace a AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) a [Správa nabídek Azure a AppSource Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [správě prostředků delegovaných v Azure](azure-delegated-resource-management.md) a o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- [Publikujte nabídky spravovaných služeb](../how-to/publish-managed-services-offers.md) pro Azure Marketplace.
