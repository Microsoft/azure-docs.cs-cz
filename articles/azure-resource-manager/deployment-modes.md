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
ms.date: 11/08/2018
ms.author: tomfitz
ms.openlocfilehash: 105a836f609859825c273ed9fba9dd46237bcaa9
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447934"
---
# <a name="azure-resource-manager-deployment-modes"></a>Režimy nasazení Azure Resource Manageru

Při nasazování prostředků, určíte, že je nasazení přírůstkové aktualizace nebo kompletní aktualizace.  Hlavní rozdíl mezi těchto dvou režimech je způsob, jakým zpracovává existujících prostředků ve skupině prostředků, které nejsou v šabloně Resource Manageru. Výchozí režim je přírůstkový.

## <a name="incremental-and-complete-deployments"></a>Přírůstkové a úplné nasazení

Při nasazování prostředků:

* V dokončení režimu Resource Manageru **odstraní** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané. Tyto prostředky, které jsou stále zadána, ale nenasadí kvůli podmínka vyhodnocena jako NEPRAVDA, nebudou však odstraněny.
* V přírůstkovém režimu Resource Manageru **ponechá beze změny** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané.

Pro oba režimy se pokusí vytvořit všechny prostředky zadané v šabloně Resource Manageru. Pokud prostředek ve skupině prostředků už existuje a jsou beze změny jeho nastavení, výsledkem operace je beze změny. Při změně hodnoty vlastnosti pro určitý prostředek, prostředek se aktualizuje s těmito novými hodnotami. Pokud se pokusíte aktualizovat umístění nebo zadejte existující prostředek, nasazení se nezdaří s chybou. Místo toho nasadit nový prostředek s umístěním nebo zadejte, že potřebujete.

Pokud opětovného nasazení prostředků v přírůstkovém režimu, zadejte všechny hodnoty vlastností pro prostředek, nejen ty, které chcete aktualizovat. Pokud nezadáte určité vlastnosti, interpretuje Resource Manageru aktualizace jako přepsání těchto hodnot.

## <a name="example-result"></a>Příklad výsledku

Pro znázornění rozdílu mezi režimy přírůstkové a úplné, vezměte v úvahu následující scénář.

**Skupina prostředků** obsahuje:

* Prostředku A
* Prostředek B
* Prostředek jazyka C

**Šablona** obsahuje:

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

```azurepowershell-interactive
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
