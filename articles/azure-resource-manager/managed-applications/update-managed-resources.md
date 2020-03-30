---
title: Aktualizace spravovaných prostředků
description: Popisuje, jak pracovat na prostředky ve skupině spravovaných prostředků pro spravovanou aplikaci Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 3dbb88d53a961e277e102d7eec580309951c73b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651212"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Práce s prostředky ve skupině spravovaných prostředků pro spravovanou aplikaci Azure

Tento článek popisuje, jak aktualizovat prostředky, které jsou nasazeny jako součást spravované aplikace. Jako vydavatel spravované aplikace máte přístup k prostředkům ve spravované skupině prostředků. Chcete-li tyto prostředky aktualizovat, je třeba najít skupinu spravovaných prostředků přidruženou ke spravované aplikaci a získat přístup k prostředku v této skupině prostředků.

Tento článek předpokládá, že jste nasadili spravovanou aplikaci ve [spravované webové aplikaci (IaaS) s](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app) ukázkovým projektem služeb azure. Tato spravovaná aplikace obsahuje **Standard_D1_v2** virtuální ho počítače. Pokud jste tuto spravovanou aplikaci nenasadili, můžete se pomocí tohoto článku seznámit s kroky aktualizace spravované skupiny prostředků.

Následující obrázek znázorňuje nasazenou spravovanou aplikaci.

![Nasazená spravovaná aplikace](./media/update-managed-resources/deployed.png)

V tomto článku pomocí azure cli:

* Identifikace spravované aplikace
* Identifikace skupiny spravovaných prostředků
* Identifikace prostředků virtuálních strojů ve skupině spravovaných prostředků
* Změna velikosti virtuálního počítače (buď na menší velikost, pokud není využita, nebo větší pro podporu většízatížení)
* Přiřazení zásady spravované skupině prostředků, která určuje povolená umístění

## <a name="get-managed-application-and-managed-resource-group"></a>Získání spravované aplikace a skupiny spravovaných prostředků

Chcete-li získat spravované aplikace ve skupině prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Chcete-li získat ID spravované skupiny prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Změna velikosti virtuálních počítače ve skupině spravovaných prostředků

Chcete-li zobrazit virtuální počítače ve skupině spravovaných prostředků, zadejte název spravované skupiny prostředků.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

Chcete-li aktualizovat velikost virtuálních počítače, použijte:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po dokončení operace ověřte, zda je aplikace spuštěna na standardu D2 v2.

![Spravovaná aplikace se standardním d2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Použití zásad u spravované skupiny prostředků

Získejte skupinu spravovaných prostředků a přiřazení zásady v tomto oboru. Zásada **e56962a6-4747-49cd-b67b-bf8b01975c4c** je vestavěná zásada pro určení povolených umístění.

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
* Ukázkové projekty najdete v [tématu Ukázkové projekty pro spravované aplikace Azure](sample-projects.md).
