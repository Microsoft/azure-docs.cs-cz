---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b9484336add0719749e9f0af56bdd70fa3906ef5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2018
ms.locfileid: "29532342"
---
Přidat dvě značky do skupiny prostředků, použijte [aktualizace skupiny az](/cli/azure/group#az_group_update) příkaz:

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Předpokládejme, že chcete přidat třetí značku. Spusťte příkaz znovu s novou značku. Připojí se k existující značky.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Prostředky nedědí značky ze skupiny prostředků. V současné době vaší skupiny prostředků má tři značky, ale prostředky nemají žádné značky. Chcete-li použít všechny značky ze skupiny prostředků pro jeho zdroje a zachovat stávající značky na prostředky, použijte následující skript:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

Alternativně můžete použít značky ze skupiny prostředků pro prostředky bez zachovat stávající značky:

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

Chcete-li sloučit několik hodnot v jedné značky, použijte řetězec formátu JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Chcete-li odebrat všechny značky na skupinu prostředků, použijte:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
