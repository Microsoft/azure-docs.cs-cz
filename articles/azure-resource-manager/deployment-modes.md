---
title: Režimy nasazení Azure Resource Manageru | Dokumentace Microsoftu
description: Popisuje, jak určit, jestli se má použít režim úplné nebo přírůstkové nasazení pomocí Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: c8c6c5499e1cea04bc5bdffbb5c07b53b96182e2
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055027"
---
# <a name="azure-resource-manager-deployment-modes"></a>Režimy nasazení Azure Resource Manageru
Při nasazování prostředků, určíte, že je nasazení přírůstkové aktualizace nebo kompletní aktualizace.  Hlavní rozdíl mezi těchto dvou režimech je způsob, jakým zpracovává existujících prostředků ve skupině prostředků, které nejsou v šabloně Resource Manageru.
Výchozí režim je přírůstkový.

## <a name="incremental-and-complete-deployments"></a>Přírůstkové a úplné nasazení
Při nasazování prostředků:

* V dokončení režimu Resource Manageru **odstraní** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané. 
* V přírůstkovém režimu Resource Manageru **ponechá beze změny** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané.

Pro oba režimy pokusí Resource Manageru zřiďte všechny prostředky v této šabloně specifikovaný. Pokud prostředek ve skupině prostředků už existuje a jsou beze změny jeho nastavení, výsledkem operace je beze změny. Pokud změníte nastavení prostředku, prostředek zřizován s těmito novými nastaveními. Pokud budete chtít aktualizovat umístění nebo zadejte existující prostředek, nasazení se nezdaří s chybou. Místo toho nasadit nový prostředek s umístěním nebo zadejte, že potřebujete.

## <a name="example-result"></a>Příklad výsledku

Pro znázornění rozdílu mezi režimy přírůstkové a úplné, vezměte v úvahu následující scénář.

**Existující skupinu prostředků** obsahuje:

* Prostředku A
* Prostředek B
* Prostředek jazyka C

**Šablona** definuje:

* Prostředku A
* Prostředek B
* Prostředek D

Při nasazení v **přírůstkové** režimu, skupina prostředků obsahuje:

* Prostředku A
* Prostředek B
* Prostředek jazyka C
* Prostředek D

Při nasazení v **kompletní** režimu C prostředků se odstraní. Skupina prostředků obsahuje:

* Prostředku A
* Prostředek B
* Prostředek D

## <a name="set-deployment-mode"></a>Nastavit režim nasazení

Chcete-li nastavit režim nasazení při nasazení pomocí Powershellu, použijte `Mode` parametru.

```powershell
New-AzureRmResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json 
```

Chcete-li nastavit režim nasazení při nasazení pomocí Azure CLI, použijte `mode` parametru.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Při použití [propojené nebo vnořené šablony](resource-group-linked-templates.md), je nutné nastavit `mode` vlastnost `Incremental`. Režim dokončení nasazení podporují pouze šablon na kořenové úrovni.

```json
"resources": [
  {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Další postup
* Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru](resource-group-authoring-templates.md).
* Další informace o nasazení prostředků najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](resource-group-template-deploy.md).
* Operace pro poskytovatele prostředků najdete v tématu [rozhraní Azure REST API](/rest/api/).

