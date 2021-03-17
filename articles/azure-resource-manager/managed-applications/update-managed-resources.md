---
title: Aktualizace spravovaných prostředků
description: Popisuje, jak pracovat s prostředky ve spravované skupině prostředků pro spravovanou aplikaci Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651212"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Práce s prostředky ve spravované skupině prostředků pro spravovanou aplikaci Azure

Tento článek popisuje, jak aktualizovat prostředky, které jsou nasazené jako součást spravované aplikace. Jako vydavatel spravované aplikace máte přístup k prostředkům ve spravované skupině prostředků. Chcete-li aktualizovat tyto prostředky, je třeba najít spravovanou skupinu prostředků přidruženou ke spravované aplikaci a získat přístup k prostředku v této skupině prostředků.

V tomto článku se předpokládá, že jste nasadili spravovanou aplikaci do [spravované webové aplikace (IaaS) s ukázkovým projektem Azure Management Services](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) . Tato spravovaná aplikace zahrnuje **Standard_D1_v2** virtuální počítač. Pokud jste tuto spravovanou aplikaci nenainstalovali, můžete i nadále používat tento článek k tomu, abyste se seznámili s postupem aktualizace spravované skupiny prostředků.

Na následujícím obrázku vidíte nasazenou spravovanou aplikaci.

![Nasazená spravovaná aplikace](./media/update-managed-resources/deployed.png)

V tomto článku použijete Azure CLI k těmto akcím:

* Identifikace spravované aplikace
* Identifikujte spravovanou skupinu prostředků.
* Identifikujte prostředky virtuálních počítačů ve spravované skupině prostředků.
* Změňte velikost virtuálního počítače (buď na menší velikost, pokud není využitá, nebo větší, aby se podporovalo větší zatížení).
* Přiřaďte zásadu spravované skupině prostředků, která určuje povolené umístění.

## <a name="get-managed-application-and-managed-resource-group"></a>Získání spravované aplikace a spravované skupiny prostředků

Pokud chcete spravované aplikace získat ve skupině prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Pokud chcete získat ID spravované skupiny prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Změna velikosti virtuálních počítačů ve spravované skupině prostředků

Pokud chcete zobrazit virtuální počítače ve spravované skupině prostředků, zadejte název spravované skupiny prostředků.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Pokud chcete aktualizovat velikost virtuálních počítačů, použijte:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po dokončení operace ověřte, že je aplikace spuštěná na standardu D2 v2.

![Spravovaná aplikace používající standardní D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Použít zásady na spravovanou skupinu prostředků

Získejte spravovanou skupinu prostředků a přiřazovat zásadu v daném oboru. Zásada **e56962a6-4747-49cd-b67b-bf8b01975c4c** je vestavěnou zásadou pro určení povolených umístění.

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

Chcete-li zobrazit povolená umístění, použijte:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Přiřazení zásad se zobrazí na portálu.

![Zobrazit přiřazení zásad](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Další kroky

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Ukázkové projekty najdete v tématu [Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
