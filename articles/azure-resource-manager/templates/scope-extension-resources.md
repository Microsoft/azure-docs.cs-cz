---
title: Obor u typů prostředků rozšíření
description: Popisuje, jak použít vlastnost Scope při nasazování typů prostředků rozšíření.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 75c2c8b8409cc9f8e7a8e71965589ece6660607a
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179975"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Nastavení oboru pro prostředky rozšíření v šablonách ARM

Prostředek rozšíření je prostředek, který upravuje jiný prostředek. Například můžete přiřadit roli k prostředku. Přiřazení role je typ prostředku rozšíření.

Úplný seznam typů prostředků rozšíření najdete v tématu [typy prostředků, které rozšiřuje možnosti jiných prostředků](../management/extension-resource-types.md).

Tento článek popisuje, jak nastavit obor pro typ prostředku rozšíření při nasazení pomocí šablony Azure Resource Manager (šablona ARM). Popisuje vlastnost oboru, která je k dispozici pro prostředky rozšíření při použití na prostředek.

> [!NOTE]
> Vlastnost oboru je dostupná pouze pro typy prostředků rozšíření. Chcete-li zadat jiný obor pro typ prostředku, který není typem rozšíření, použijte vnořené nebo propojené nasazení. Další informace najdete v tématech [nasazení skupin prostředků](deploy-to-resource-group.md), [nasazení předplatných](deploy-to-subscription.md), [nasazení skupin pro správu](deploy-to-management-group.md)a [nasazení klientů](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Použít v oboru nasazení

Chcete-li použít typ prostředku rozšíření v cílovém oboru nasazení, přidejte prostředek do šablony, stejně jako jakýkoli typ prostředku. Dostupné obory jsou [skupiny prostředků](deploy-to-resource-group.md), [předplatné](deploy-to-subscription.md), [skupina pro správu](deploy-to-management-group.md)a [tenant](deploy-to-tenant.md). Rozsah nasazení musí podporovat typ prostředku.

Následující šablona nasadí zámek.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Při nasazení do skupiny prostředků zamkne skupinu prostředků.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

Následující příklad přiřadí roli.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Při nasazení do předplatného přiřadí role k předplatnému.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Použít u prostředku

Chcete-li použít prostředek rozšíření na prostředek, použijte `scope` vlastnost. Nastavte vlastnost Scope na název prostředku, pro který přidáváte rozšíření. Vlastnost Scope je kořenová vlastnost pro typ prostředku rozšíření.

Následující příklad vytvoří účet úložiště a použije pro něj roli.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Další kroky

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
