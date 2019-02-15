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
ms.date: 02/13/2019
ms.author: tomfitz
ms.openlocfilehash: bc28349e1bfc935ac8298f991575c1e0cb42d38c
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299223"
---
# <a name="azure-resource-manager-deployment-modes"></a>Režimy nasazení Azure Resource Manageru

Při nasazování prostředků, určíte, že je nasazení přírůstkové aktualizace nebo kompletní aktualizace.  Hlavní rozdíl mezi těchto dvou režimech je způsob, jakým zpracovává existujících prostředků ve skupině prostředků, které nejsou v šabloně Resource Manageru. Výchozí režim je přírůstkový.

Pro oba režimy se pokusí vytvořit všechny prostředky zadané v šabloně Resource Manageru. Není-li prostředek již existuje ve skupině prostředků a její nastavení jsou beze změny, žádná operace provedena pro daný prostředek. Při změně hodnoty vlastnosti pro určitý prostředek, prostředek se aktualizuje s těmito novými hodnotami. Pokud se pokusíte aktualizovat umístění nebo zadejte existující prostředek, nasazení se nezdaří s chybou. Místo toho nasadit nový prostředek s umístěním nebo zadejte, že potřebujete.

## <a name="complete-mode"></a>Úplný režim

V dokončení režimu Resource Manageru **odstraní** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané. Prostředky, které jsou v této šabloně specifikovaný, ale není nasazena, protože [podmínku](resource-manager-templates-resources.md#condition) vyhodnotí na hodnotu false, nebudou odstraněny.

Existují některé rozdíly v jak postupovat při odstranění úplný režim typy prostředků. Nadřazené prostředky jsou po není v šabloně, která je nasazena v režimu dokončení automaticky odstraní. Pokud není v šabloně nejsou automaticky odstraněny některé podřízené prostředky. Těchto podřízených prostředků se odstraní, ale pokud se nadřazený prostředek odstraní. 

Například pokud vaše skupina prostředků obsahuje zóny DNS (typ prostředku Microsoft.Network/dnsZones) a záznam CNAME (typ prostředku Microsoft.Network/dnsZones/CNAME), zóny DNS je nadřazeným prostředkem pro záznam CNAME. Pokud nasadíte s režimem dokončení a nezahrnují zóny DNS v šabloně, zóny DNS a záznamu CNAME se obě odstraní. Pokud do šablony zahrnout zónu DNS, ale neobsahují záznam CNAME, záznam CNAME se neodstraní. 

Seznam jak postupovat při odstraňování typů prostředků najdete v tématu [odstranění Azure prostředky pro nasazení úplný režim](complete-mode-deletion.md).

> [!NOTE]
> Režim dokončení nasazení podporují pouze šablon na kořenové úrovni. Pro [propojené nebo vnořené šablony](resource-group-linked-templates.md), je nutné použít přírůstkový režim. 
>

## <a name="incremental-mode"></a>Přírůstkový režim

V přírůstkovém režimu Resource Manageru **ponechá beze změny** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané. Pokud opětovného nasazení prostředků v přírůstkovém režimu, zadejte všechny hodnoty vlastností pro prostředek, nejen ty, které chcete aktualizovat. Pokud nezadáte určité vlastnosti, interpretuje Resource Manageru aktualizace jako přepsání těchto hodnot.

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
New-AzResourceGroupDeployment `
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

Následující příklad ukazuje šablonu propojené nastavený režim přírůstkové nasazení:

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
