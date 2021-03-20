---
title: Dodržování předpisů pomocí Azure Policy
description: Přiřaďte předdefinované zásady v Azure Policy k auditování dodržování předpisů prostředků služby signalizace Azure.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89295011"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Audit dodržování předpisů u prostředků služby signalizace Azure pomocí Azure Policy

[Azure Policy](../governance/policy/overview.md) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb.

Tento článek představuje předdefinované zásady (Preview) pro službu Azure Signal Service. Pomocí těchto zásad můžete auditovat nové a stávající prostředky signálů pro dodržování předpisů.

Za použití Azure Policy se neúčtují žádné poplatky.

## <a name="built-in-policy-definitions"></a>Předdefinované definice zásad

Následující předdefinované definice zásad jsou specifické pro službu Azure Signal Service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Přiřazení definic zásad

* Přiřaďte definice zásad pomocí [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [šablony Správce prostředků](../governance/policy/assign-policy-template.md)nebo Azure Policy sady SDK.
* Umožňuje nastavit obor přiřazení zásady na skupinu prostředků, předplatné nebo [skupinu pro správu Azure](../governance/management-groups/overview.md). Přiřazení zásad signalizace platí pro existující a nové prostředky signalizace v rámci oboru.
* Povolte nebo zakažte [vynucení zásad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) kdykoli.

> [!NOTE]
> Až zásadu přiřadíte nebo aktualizujete, může trvat nějakou dobu, než se přiřazení použije na prostředky v definovaném oboru. Podívejte se na informace o [aktivačních událostech pro vyhodnocení zásad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Kontrola dodržování zásad

Přístup k informacím o dodržování předpisů generovanými přiřazeními zásad pomocí Azure Portal, nástrojů příkazového řádku Azure nebo sad Azure Policy SDK. Podrobnosti najdete v tématu o [získání dat o dodržování předpisů u prostředků Azure](../governance/policy/how-to/get-compliance-data.md).

Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Chcete-li určit důvod nebo najít odpovědné změny, přečtěte si téma [určení nedodržení předpisů](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Dodržování zásad na portálu:

1. Vyberte **všechny služby** a vyhledejte **zásady**.
1. Vyberte možnost **dodržování předpisů**.
1. Omezení stavů dodržování předpisů nebo hledání zásad pomocí filtrů
   
    [![Dodržování zásad na portálu ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Vyberte zásadu pro kontrolu agregovaných podrobností a událostí dodržování předpisů. V případě potřeby vyberte konkrétní signál pro dodržování předpisů s prostředky.

### <a name="policy-compliance-in-the-azure-cli"></a>Dodržování zásad v rozhraní příkazového řádku Azure

K získání dat o dodržování předpisů můžete použít také rozhraní příkazového řádku Azure. Pomocí příkazu [AZ Policy list](/cli/azure/policy/assignment#az-policy-assignment-list) v rozhraní PŘÍKAZového řádku můžete například získat ID zásad pro zásady služby Azure Signal, které se použijí:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Ukázkový výstup:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Pak spusťte příkaz [AZ Policy list](/cli/azure/policy/state#az-policy-state-list) a vraťte stav dodržování předpisů ve formátu JSON pro všechny prostředky v určité skupině prostředků:

```azurecli
az policy state list --g <resourceGroup>
```

Nebo spusťte [AZ Policy list](/cli/azure/policy/state#az-policy-state-list) a vraťte stav dodržování předpisů ve formátu JSON konkrétního prostředku signalizace:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Další kroky

* Další informace o [definicích](../governance/policy/concepts/definition-structure.md) a [důsledcích](../governance/policy/concepts/effects.md) Azure Policy

* Vytvoření [vlastní definice zásady](../governance/policy/tutorials/create-custom-policy-definition.md)

* Další informace o [funkcích zásad správného řízení](../governance/index.yml) v Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/