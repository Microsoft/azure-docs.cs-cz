---
title: Dodržování předpisů pomocí Azure Policy
description: Přiřazení předdefinovaných zásad v Azure Policy pro audit dodržování předpisů ve službě Azure Container Registry
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 26c56616bcc411063d0ebfda28ba1e6fdf44c7fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291007"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Audit dodržování předpisů pro služby Azure Container Registry pomocí Azure Policy

[Azure Policy](../governance/policy/overview.md) je služba v Azure, kterou používáte k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb.

Tento článek představuje předdefinované zásady pro Azure Container Registry. Pomocí těchto zásad můžete auditovat nové a stávající registry na dodržování předpisů.

Za použití Azure Policy se neúčtují žádné poplatky.

## <a name="built-in-policy-definitions"></a>Předdefinované definice zásad

Následující předdefinované definice zásad jsou specifické pro Azure Container Registry:

[!INCLUDE [azure-policy-reference-policies-container-registry](../../includes/policy/reference/bycat/policies-container-registry.md)]

Viz také integrovaná definice zásad sítě: [Container Registry by měl používat koncový bod služby virtuální sítě](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78).

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
1. Použijte filtry k omezení stavu dodržování předpisů nebo k vyhledání zásad.

    ![Dodržování zásad na portálu](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
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
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
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

* Přečtěte si další informace o Azure Policy [definice](../governance/policy/concepts/definition-structure.md) a [efekty](../governance/policy/concepts/effects.md).

* Vytvořte [vlastní definici zásad](../governance/policy/tutorials/create-custom-policy-definition.md).

* Přečtěte si další informace o [funkcích zásad správného řízení](../governance/index.yml) v Azure.
