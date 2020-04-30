---
title: Dodržování předpisů pomocí Azure Policy
description: Přiřazení předdefinovaných zásad v Azure Policy pro audit dodržování předpisů ve službě Azure Container Registry
ms.topic: article
ms.date: 02/26/2020
ms.openlocfilehash: a2bfdc18f4bbf16fe8fa6bcbcba7bab18aedabf1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145000"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy

[Azure Policy](../governance/policy/overview.md) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb.

Tento článek představuje předdefinované zásady (Preview) pro Azure Container Registry. Pomocí těchto zásad můžete auditovat nové a stávající registry na dodržování předpisů.

Za použití Azure Policy se neúčtují žádné poplatky.

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="built-in-policy-definitions"></a>Předdefinované definice zásad

Následující předdefinované definice zásad jsou specifické pro Azure Container Registry:

[!INCLUDE [azure-policy-samples-policies-container-registry](../../includes/policy/samples/bycat/policies-container-registry.md)]

Viz také integrovaná definice zásad sítě: [[Preview] Container Registry by měla používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

## <a name="assign-policies"></a>Přiřazování zásad

* Přiřaďte zásady pomocí [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), [Správce prostředků šablony](../governance/policy/assign-policy-template.md)nebo sady SDK Azure Policy.
* Umožňuje nastavit obor přiřazení zásady na skupinu prostředků, předplatné nebo [skupinu pro správu Azure](../governance/management-groups/overview.md). Přiřazení zásad registru kontejneru se vztahují na existující a nové Registry kontejneru v rámci oboru.
* Povolte nebo zakažte [vynucení zásad](../governance/policy/concepts/assignment-structure.md#enforcement-mode) kdykoli.

> [!NOTE]
> Až zásadu přiřadíte nebo aktualizujete, může trvat nějakou dobu, než se přiřazení použije na prostředky v definovaném oboru. Podívejte se na informace o [aktivačních událostech pro vyhodnocení zásad](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Kontrola dodržování zásad

Přístup k informacím o dodržování předpisů generovanými přiřazeními zásad pomocí Azure Portal, nástrojů příkazového řádku Azure nebo sad Azure Policy SDK. Podrobnosti najdete v tématu o [získání dat o dodržování předpisů u prostředků Azure](../governance/policy/how-to/get-compliance-data.md).

Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Chcete-li určit důvod nebo najít odpovědné změny, přečtěte si téma [určení nedodržení předpisů](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Dodržování zásad na portálu:

1. Vyberte **všechny služby**a vyhledejte **zásady**.
1. Vyberte možnost **dodržování předpisů**.
1. Pomocí filtrů můžete omezit stavy dodržování předpisů nebo vyhledat zásady dodržování ![zásad na portálu.](./media/container-registry-azure-policy/azure-policy-compliance.png)
1. Vyberte zásadu pro kontrolu agregovaných podrobností a událostí dodržování předpisů. V případě potřeby vyberte konkrétní registr pro dodržování předpisů pomocí prostředků.

### <a name="policy-compliance-in-the-azure-cli"></a>Dodržování zásad v rozhraní příkazového řádku Azure

K získání dat o dodržování předpisů můžete použít také rozhraní příkazového řádku Azure. Pomocí příkazu [AZ Policy list](/cli/azure/policy/assignment#az-policy-assignment-list) v rozhraní PŘÍKAZového řádku můžete například získat id zásad Azure Container Registry zásad, které se použijí:

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

Pak spusťte příkaz [AZ Policy list](/cli/azure/policy/state#az-policy-state-list) a vraťte stav dodržování předpisů ve formátu JSON pro všechny prostředky v rámci konkrétního ID zásad:

```azurecli
az policy state list \
  --resource <policyID>
```

Nebo spusťte [AZ Policy list](/cli/azure/policy/state#az-policy-state-list) a vraťte stav dodržování předpisů ve formátu JSON určitého prostředku registru, jako je například *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Další kroky

* Další informace o [definicích](../governance/policy/concepts/definition-structure.md) a [důsledcích](../governance/policy/concepts/effects.md) Azure Policy

* Vytvoření [vlastní definice zásady](../governance/policy/tutorials/create-custom-policy-definition.md)

* Další informace o [funkcích zásad správného řízení](../governance/index.yml) v Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/