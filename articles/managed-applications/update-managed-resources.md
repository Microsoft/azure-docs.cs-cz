---
title: Aktualizovat prostředky v Azure spravované aplikace | Dokumentace Microsoftu
description: Popisuje, jak pracovat s prostředky v spravovanou skupinu prostředků Azure spravované aplikace.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 21f4e0aa339eb0c746f9b9b06f8aaada6c4d4b71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61043405"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Pracovat s prostředky v spravovanou skupinu prostředků Azure spravované aplikace

Tento článek popisuje, jak aktualizovat prostředky, které jsou nasazované jako součást spravované aplikace. Jako vydavatel spravované aplikace budete mít přístup k prostředkům ve spravované skupině prostředků. K aktualizaci těchto prostředků, budete muset najít spravované skupině prostředků přidružené ke spravované aplikaci a přístup k prostředku v této skupině prostředků.

Tento článek předpokládá, že jste nasadili spravované aplikace v [spravovaných webových aplikací (IaaS) pomocí služeb Azure pro správu](https://github.com/Azure/azure-managedapp-samples/tree/master/samples/201-managed-web-app) ukázkového projektu. Spravované aplikace zahrnuje **Standard_D1_v2** virtuálního počítače. Pokud jste nenasadili tuto spravovanou aplikaci, můžete stále použít v tomto článku a seznamte se s kroky pro aktualizaci spravované skupiny prostředků.

Následující obrázek ukazuje nasazené spravované aplikace.

![Nasazení spravované aplikace](./media/update-managed-resources/deployed.png)

V tomto článku pomocí Azure CLI:

* Identifikujte spravované aplikace
* Identifikujte spravovanou skupinu prostředků
* Identifikujte prostředky virtuálních počítačů ve spravované skupině prostředků
* Změna velikosti virtuálního počítače (buď menší velikost, pokud nepoužívá, nebo větší pro podporu větší zatížení)
* Přiřazení zásad na spravovanou skupinu prostředků, která určuje povolené lokality

## <a name="get-managed-application-and-managed-resource-group"></a>Spravované aplikace a spravované skupiny prostředků

K získání spravovaných aplikací ve skupině prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

Pokud chcete získat ID spravovanou skupinu prostředků, použijte:

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>Změna velikosti virtuálních počítačů ve spravované skupině prostředků

Pokud chcete zobrazit virtuálních počítačů ve spravované skupině prostředků, zadejte název spravované skupině prostředků.

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

K aktualizaci velikosti virtuálních počítačů, použijte:

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

Po dokončení operace, ověřte, že aplikace běží na Standard D2 v2.

![Spravované aplikace pomocí Standard D2 v2](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>Použít zásady pro spravované skupiny prostředků

Načte spravované skupiny prostředků a přiřazení zásady v daném oboru. Zásady **e56962a6-4747-49cd-b67b-bf8b01975c4c** jsou integrované zásady pro určení povolených umístění.

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

Pokud chcete zobrazit povolených umístění, použijte:

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

Přiřazení zásad se zobrazí na portálu.

![Zobrazení přiřazení zásady](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>Další postup

* Úvod ke spravovaným aplikacím najdete v [přehledu spravovaných aplikací](overview.md).
* Ukázkové projekty, naleznete v tématu [ukázkových projektů Azure spravované aplikace](sample-projects.md).
