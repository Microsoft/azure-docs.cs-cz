---
title: Režimy nasazení
description: Popisuje, jak určit, jestli se má použít kompletní nebo přírůstkový režim nasazení s Azure Resource Manager.
ms.topic: conceptual
ms.date: 12/23/2019
ms.openlocfilehash: dc5446c56c92b61016563995ebc4c884d48e2419
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152387"
---
# <a name="azure-resource-manager-deployment-modes"></a>Režimy nasazení Azure Resource Manager

Při nasazování prostředků určíte, že nasazení je přírůstková nebo kompletní aktualizace.  Rozdíl mezi těmito dvěma režimy spočívá v tom, že Správce prostředků zpracovává stávající prostředky ve skupině prostředků, která není v šabloně. Výchozí režim je přírůstkový.

V obou režimech se Správce prostředků pokusí vytvořit všechny prostředky, které jsou uvedené v šabloně. Pokud prostředek ve skupině prostředků už existuje a jeho nastavení se nezměnilo, pro tento prostředek se nebere žádná operace. Pokud změníte hodnoty vlastností prostředku, prostředek se aktualizuje o tyto nové hodnoty. Pokud se pokusíte aktualizovat umístění nebo typ existujícího prostředku, nasazení dojde k chybě. Místo toho nasaďte nový prostředek s umístěním nebo typem, který potřebujete.

## <a name="complete-mode"></a>Režim dokončení

V režimu úplné Správce prostředků **odstraní** prostředky, které existují ve skupině prostředků, ale nejsou v šabloně zadané.

Pokud vaše šablona obsahuje prostředek, který není nasazený, protože [Podmínka](conditional-resource-deployment.md) je vyhodnocena jako NEPRAVDA, výsledek závisí na tom, která REST API verze, kterou použijete k nasazení šablony. Pokud používáte verzi starší než 2019-05-10, prostředek se **neodstraní**. Při 2019-05-10 nebo novějším se prostředek **odstraní**. Nejnovější verze Azure PowerShell a Azure CLI odstraní prostředek.

Použití kompletního režimu s [kopírováním smyček](create-multiple-instances.md)vám bude opatrní. Všechny prostředky, které nejsou zadány v šabloně po vyřešení smyčky kopírování, jsou odstraněny.

Pokud nasadíte do [více než jedné skupiny prostředků v šabloně](cross-resource-group-deployment.md), prostředky ve skupině prostředků zadané v rámci operace nasazení mají nárok na odstranění. Prostředky v sekundárních skupinách prostředků se neodstraňují.

Existují určité rozdíly ve způsobu, jakým typy prostředků zpracovávají kompletní odstranění režimu. Nadřazené prostředky se odstraní automaticky, když nejsou v šabloně, která je nasazena v úplném režimu. Některé podřízené prostředky nejsou automaticky odstraněny, pokud nejsou v šabloně. Tyto podřízené prostředky se ale odstraní, pokud se odstraní nadřazený prostředek.

Například pokud vaše skupina prostředků obsahuje zónu DNS (typ prostředku Microsoft. Network/dnsZones) a záznam CNAME (typ prostředku Microsoft. Network/dnsZones/CNAME), zóna DNS je nadřazeným prostředkem pro záznam CNAME. Pokud nasadíte nástroj s úplným režimem a nezadáte do šablony zónu DNS, odstraní se zóna DNS a záznam CNAME. Pokud zahrnete zónu DNS do šablony, ale nezahrnete záznam CNAME, záznam CNAME se neodstraní.

Seznam způsobu, jakým se zpracovávají typy prostředků, najdete v tématu [odstranění prostředků Azure pro nasazení v režimu úplného režimu](complete-mode-deletion.md).

Pokud je skupina prostředků [zamčená](../management/lock-resources.md), režim úplného režimu neodstraní prostředky.

> [!NOTE]
> Pouze šablony na kořenové úrovni podporují režim úplného nasazení. U [propojených nebo vnořených šablon](linked-templates.md)je nutné použít přírůstkový režim.
>
> [Nasazení na úrovni předplatného](deploy-to-subscription.md) nepodporují režim úplného přístupu.
>
> Portál v současné době nepodporuje režim úplného režimu.
>

## <a name="incremental-mode"></a>Přírůstkový režim

V přírůstkovém režimu Správce prostředků **opustí nezměněné** prostředky, které existují ve skupině prostředků, ale nejsou zadané v šabloně. Prostředky v šabloně **se přidají** do skupiny prostředků.

Je důležité si uvědomit, že přírůstkový režim se vztahuje na celý prostředek, nikoli na jednotlivé vlastnosti stávajícího prostředku. Při opětovném nasazení stávajícího prostředku v přírůstkovém režimu jsou znovu aplikovány všechny vlastnosti. **Vlastnosti se přírůstkově přidávají**. Běžným srozumitelným pochopením je, že si nezůstane beze změny vlastností, které nejsou zadané v šabloně. Pokud nezadáte určité vlastnosti, Správce prostředků interpretuje nasazení jako přepsání těchto hodnot. Vlastnosti, které nejsou zahrnuté v šabloně, se obnoví na výchozí hodnoty nastavené poskytovatelem prostředků. Zadejte všechny jiné než výchozí hodnoty pro prostředek, nikoli pouze ty, které aktualizujete. Definice prostředků v šabloně vždy obsahuje konečný stav prostředku. Nemůže představovat částečnou aktualizaci stávajícího prostředku.

## <a name="example-result"></a>Příklad výsledku

K ilustraci rozdílu mezi režimy přírůstkového a úplného použití zvažte následující scénář.

**Skupina prostředků** obsahuje:

* Prostředek A
* Prostředek B
* Prostředek C

**Šablona** obsahuje:

* Prostředek A
* Prostředek B
* Prostředek D

Při nasazení v **přírůstkovém** režimu má skupina prostředků:

* Prostředek A
* Prostředek B
* Prostředek C
* Prostředek D

Při nasazení v režimu **úplné** se odstraní prostředek C. Skupina prostředků má:

* Prostředek A
* Prostředek B
* Prostředek D

## <a name="set-deployment-mode"></a>Nastavit režim nasazení

Chcete-li nastavit režim nasazení při nasazení pomocí prostředí PowerShell, použijte parametr `Mode`.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Pokud chcete nastavit režim nasazení při nasazení pomocí Azure CLI, použijte parametr `mode`.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Následující příklad ukazuje propojenou šablonu nastavenou na režim přírůstkového nasazení:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Další kroky

* Další informace o vytváření šablon Správce prostředků najdete v tématu [vytváření šablon Azure Resource Manager](template-syntax.md).
* Další informace o nasazení prostředků najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).
* Pokud chcete zobrazit operace pro poskytovatele prostředků, přečtěte si článek [Azure REST API](/rest/api/).
