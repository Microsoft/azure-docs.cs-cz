---
title: Dodržování předpisů pomocí zásad Azure
description: Přiřazení integrovaných zásad v zásadách Azure k auditování dodržování předpisů registrů kontejnerů Azure
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: 012cd013de1c60fddcfb28e4bca96d761ada41ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330732"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Auditování dodržování předpisů registrů kontejnerů Azure pomocí zásad Azure

[Azure Policy](../governance/policy/overview.md) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb.

Tento článek zavádí integrované zásady (preview) pro Azure Container Registry. Tyto zásady slouží k auditu dodržování předpisů novými a stávajícími registry.

Za používání Zásad Azure se neplatí žádné poplatky.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="built-in-policy-definitions"></a>Předdefinované definice zásad

Následující definice předdefinovaných zásad jsou specifické pro Azure Container Registry:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/azure-policy-samples-policies-container-registry.md)]

Viz také definice zásad sítě: [[Preview] Kontejner Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Přiřazování zásad

* Přiřazuji zásady pomocí [portálu Azure](../governance/policy/assign-policy-portal.md), [příkazového příkazu KON ,](../governance/policy/assign-policy-azurecli.md) [šablony Správce prostředků](../governance/policy/assign-policy-template.md)nebo sad Azure Policy SDK.
* Obor přiřazení zásad ke skupině prostředků, předplatnému nebo [skupině pro správu Azure](../governance/management-groups/overview.md). Přiřazení zásad registru kontejnerů platí pro existující a nové registry kontejnerů v rámci oboru.
* Kdykoli povolte nebo zakažte [vynucení zásad.](../governance/policy/concepts/assignment-structure.md#enforcement-mode)

> [!NOTE]
> Po přiřazení nebo aktualizaci zásady trvá určité časové nastavení, než bude přiřazení použito u zdrojů v definovaném oboru. Zobrazit informace o [aktivačních událostech vyhodnocení zásad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Kontrola dodržování zásad

Získejte přístup k informacím o dodržování předpisů generovaných přiřazeními zásad pomocí portálu Azure, nástrojů příkazového řádku Azure nebo sad Azure Policy SDK. Podrobnosti najdete v [tématu Získání dat o dodržování předpisů prostředků Azure](../governance/policy/how-to/get-compliance-data.md).

Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Důvod určení nebo zjištění odpovědnosti za změnu naleznete v [tématu Určení nesouladu](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Dodržování zásad na portálu:

1. Vyberte **Všechny služby**a vyhledejte **zásady**.
1. Vyberte **možnost Dodržování předpisů**.
1. Pomocí filtrů můžete omezit stavy ![dodržování předpisů](./media/container-registry-azure-policy/azure-policy-compliance.png)nebo vyhledat dodržování zásad na portálu .
1. Vyberte zásadu, která zkontroluje agregované podrobnosti a události dodržování předpisů. V případě potřeby vyberte konkrétní registr pro dodržování předpisů prostředků.

### <a name="policy-compliance-in-the-azure-cli"></a>Dodržování zásad v nastavení nastavení nastavení řízení o Azure

Můžete také použít Azure CLI k získání dat dodržování předpisů. Pomocí příkazu [seznamu přiřazení zásad az](/cli/azure/policy/assignment#az-policy-assignment-list) v příkazovém příkazu k získání ID zásad zásad zásad registru kontejnerů Azure, které se použijí:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Ukázkový výstup:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
[Preview]: Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Potom spusťte [seznam stavu zásad az,](/cli/azure/policy/state#az-policy-state-list) chcete-li vrátit stav dodržování předpisů ve formátu JSON pro všechny prostředky v rámci konkrétního ID zásad:

```azurecli
az policy state list \
  --resource <policyID>
```

Nebo spusťte [seznam stavu zásad az](/cli/azure/policy/state#az-policy-state-list) avrátíte stav kompatibility ve formátu JSON určitého prostředku registru, například *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

* Další informace o [definicích](../governance/policy/concepts/definition-structure.md) a [efektech](../governance/policy/concepts/effects.md) zásad Azure

* Vytvoření [vlastní definice zásad](../governance/policy/tutorials/create-custom-policy-definition.md)

* Další informace o [možnostech zásad správného řízení](../governance/index.yml) v Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/