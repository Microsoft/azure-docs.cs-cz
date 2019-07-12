---
title: Spravované služby nabídky na webu Azure Marketplace
description: Spravované služby, které nabízí umožňují poskytovatelům služeb resource management nabízí prodávat zákazníkům na webu Azure Marketplace.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a6fcf5f1d0ac194d60f834fb8d26db019c538410
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809873"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Spravované služby nabídky na webu Azure Marketplace

Tento článek popisuje nové **služby spravované** typ v nabídky [Azure Marketplace](https://azuremarketplace.microsoft.com). Spravované služby, které nabízí umožňují zákazníkům se správou prostředků Azure delegované nabídky prostředků správy služeb. Pro všechny potenciální zákazníky nebo pouze pro jeden nebo více konkrétní zákazníky, proveďte tyto nabídky k dispozici. Protože jste vyúčtování pro zákazníky přímo za náklady na tyto spravované služby, neexistují žádné poplatky účtovat od Microsoftu.

## <a name="understand-managed-services-offers"></a>Vysvětlení nabízí spravované služby

Nabízí spravované služby zjednodušit proces zprovoznění zákazníků pro delegovanou správu prostředků Azure. Jakmile zákazník koupí nabídky na webu Azure Marketplace, budou moct určit, které předplatných nebo skupinách prostředků by měl být připojili tak, aby zadali uživatelé ve vaší organizaci může provádět úkoly správy pro zákazníky v rámci vaší tenanta organizace.

Po tomto žádná další akce nevyžadovala zákazník nebo poskytovatel služeb zobrazíte zprovoznění zákazníků. Důvodem je, že při definování nabídky v [portál partnerů cloudu](https://cloudpartner.azure.com/), vytvoření manifestu, který určuje uživatele Azure AD, skupiny a zásady služby, které budou mít přístup k prostředkům zákazníka pomocí Azure delegovat prostředků Správa. společně s rolí, které definují jejich úroveň přístupu. Pomocí přiřazení oprávnění pro skupiny služby Azure AD, spíše než řadu jednotlivých uživatelů nebo aplikací účty, můžete přidat nebo odebrat jednotlivé uživatele, když se změní vaše požadavky na přístup.

## <a name="public-and-private-offers"></a>Veřejné a soukromé nabídky

Každá nabídka spravované služby obsahuje jeden nebo více plánů. Tyto plány můžou být privátní nebo veřejné.

Pokud chcete omezit nabídky pro konkrétní zákazníky, můžete publikovat privátní plánu. Pokud tak učiníte, můžete plán koupit jenom pro konkrétní] ID, které poskytnete předplatných. Další informace najdete v tématu [soukromé nabídky](https://docs.microsoft.com/azure/marketplace/private-offers).

Veřejné plány umožňují zvýšit úroveň služby pro nové zákazníky. Toto jsou obvykle vhodnější, když potřebujete pouze omezený přístup do tenanta zákazníka. Po vytvoření vztah se zákazníkem, pokud se rozhodnou k udělení přístupu Další vaší organizace, můžete to udělat tak, že publikování nového plánu privátní pouze tohoto zákazníka nebo podle [registrace pro další přístupu pomocí Azure Šablony Resource Manageru](../how-to/onboard-customer.md).

Uvědomte si, že po plán se publikoval jako veřejné, nebudete moct změnit ho na soukromou. Kromě toho nemůžete omezit dostupnost veřejný plán některým zákazníkům nebo do určitého počtu zákazníků, i když můžete zastavit a prodej plánu úplně, pokud se rozhodnete tak učinit.

V případě potřeby, můžete zahrnout veřejné a soukromé plánů ve stejné nabídce.

## <a name="publish-managed-service-offers"></a>Publikování nabídky spravované služby

Informace o publikování nabídky na spravované služby najdete v tématu [publikování nabídky služby spravované Azure Marketplace](../how-to/publish-managed-services-offers.md). Obecné informace o publikování na webu Azure Marketplace pomocí portálu partnerů cloudu najdete v tématu [Azure Marketplace a AppSource Průvodce publikováním](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) a [nabídky Spravovat Azure a AppSource Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

## <a name="next-steps"></a>Další postup

- Další informace o [delegovat správu prostředků Azure](azure-delegated-resource-management.md) a [napříč tenanty činnosti správy](cross-tenant-management-experience.md).
- [Publikování nabídky spravovaných služeb](../how-to/publish-managed-services-offers.md) na webu Azure Marketplace.
