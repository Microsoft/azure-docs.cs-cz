---
title: Nabídky spravovaných služeb na Azure Marketplace
description: Spravované služby umožňují poskytovatelům služeb prodávat nabídky správy prostředků zákazníkům v Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453587"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Nabídky spravovaných služeb na Azure Marketplace

Tento článek popisuje nový typ nabídky **spravované služby** v [Azure Marketplace](https://azuremarketplace.microsoft.com). Služby spravovaných služeb vám umožní nabízet služby správy prostředků zákazníkům se správou delegovaných prostředků Azure. Tyto nabídky můžete zpřístupnit všem potenciálním zákazníkům nebo jenom jednomu nebo více konkrétním zákazníkům. Vzhledem k tomu, že se zákazníci fakturují přímo na náklady související s těmito spravovanými službami, neúčtují žádné poplatky za vás Microsoft.

## <a name="understand-managed-services-offers"></a>Vysvětlení nabídek spravovaných služeb

Spravované služby nabízí zjednodušení procesu připojování zákazníků pro správu delegovaných prostředků Azure. Když zákazník koupí nabídku v Azure Marketplace, bude moct určit, které předplatné nebo skupiny prostředků by se měly připojit. Všimněte si, že odběr musí být nejdřív autorizovaný pro registraci, a to ruční registrací poskytovatele prostředků **Microsoft. ManagedServices** .

Potom budou uživatelé ve vaší organizaci moci provádět úlohy správy pro tyto prostředky z tenanta vaší organizace podle přístupu, který jste definovali při vytváření nabídky v [portál partnerů cloudu](https://cloudpartner.azure.com/). To se provádí pomocí manifestu, který určuje uživatele, skupiny a instanční objekty služby Azure AD, které budou mít přístup k zákaznickým prostředkům pomocí delegované správy prostředků Azure, spolu s rolemi, které definují jejich úroveň přístupu. Když pro skupinu Azure AD přiřadíte oprávnění místo řady individuálních účtů uživatelů nebo aplikací, můžete přidat nebo odebrat jednotlivé uživatele, když se vaše požadavky na přístup změní.

## <a name="public-and-private-offers"></a>Veřejné a soukromé nabídky

Každá nabídka spravovaných služeb obsahuje jeden nebo více plánů. Plány můžou být buď soukromé, nebo veřejné. 

Pokud chcete omezit nabídku na konkrétní zákazníky, můžete publikovat soukromý plán. V takovém případě lze plán zakoupit pouze pro konkrétní ID předplatného, které zadáte. Další informace najdete v tématu [soukromé nabídky](../../marketplace/private-offers.md).

Veřejné plány umožňují propagovat vaše služby na nové zákazníky. To je obvykle vhodnější, pokud požadujete pouze omezený přístup k tenantovi zákazníka. Po navázání vztahu se zákazníkem, pokud se rozhodne udělit vaší organizaci dodatečný přístup, můžete to udělat buď publikováním nového privátního plánu jenom pro tohoto zákazníka, nebo jeho [zprovozněním pro další přístup pomocí šablon Azure Resource Manager](../how-to/onboard-customer.md).

V případě potřeby můžete do stejné nabídky zahrnout jak veřejné, tak i privátní plány.

> [!IMPORTANT]
> Jakmile je plán publikovaný jako veřejný, nemůžete ho změnit na privátní. Chcete-li určit, kteří zákazníci mohou přijmout vaši nabídku a delegovat prostředky, použijte privátní plán. S veřejným plánem nemůžete omezit dostupnost na konkrétní zákazníky nebo dokonce na určitý počet zákazníků (i když se rozhodnete, že můžete plán úplně zastavit). V současné době není k dispozici žádný mechanismus pro zamítnutí nebo odebrání delegování, když zákazník nabídku přijme, i když se můžete kdykoli obrátit na zákazníka a požádat ho, aby [Váš přístup odebral](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publikování nabídek spravovaných služeb

Informace o tom, jak publikovat nabídku spravovaných služeb, najdete v tématu [publikování nabídky spravovaných služeb na Azure Marketplace](../how-to/publish-managed-services-offers.md). Obecné informace o publikování Azure Marketplace pomocí portál partnerů cloudu najdete v tématu Příručka pro [publikování Azure Marketplace a AppSource](../../marketplace/marketplace-publishers-guide.md) a [Správa nabídek Azure a AppSource Marketplace](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [správě prostředků delegovaných v Azure](azure-delegated-resource-management.md) a o [prostředích pro správu mezi klienty](cross-tenant-management-experience.md).
- [Publikujte nabídky spravovaných služeb](../how-to/publish-managed-services-offers.md) pro Azure Marketplace.
