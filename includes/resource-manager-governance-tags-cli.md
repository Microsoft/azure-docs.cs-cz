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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38752655"
---
Pokud chcete do skupiny prostředků přidat dvě značky, použijte příkaz [az group update](/cli/azure/group#az_group_update):

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

Předpokládejme, že chcete přidat třetí značku. Spusťte tento příkaz znovu s novou značku. Připojí se k existujícím značkám.

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

Prostředky nedědí značky ze skupiny prostředků. Vaše skupiny prostředků momentálně má tři značky, ale prostředky nemají žádné. Pokud chcete na příslušné prostředky použít všechny značky ze skupiny prostředků a zachovat u prostředků stávající značky, použijte tento skript:

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

Další možností je použít pro prostředky značky ze skupiny prostředků a nezachovávat stávající značky:

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

Pokud chcete sloučit několik hodnot v jedné značce, použijte řetězec JSON.

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

Pokud chcete odebrat všechny značky ve skupině prostředků, použijte příkaz:

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
