---
title: Režimy nasazení
description: Popisuje, jak určit, jestli se má použít režim úplného nebo přírůstkového nasazení ve Správci prostředků Azure.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460241"
---
# <a name="azure-resource-manager-deployment-modes"></a>Režimy nasazení Azure Resource Manageru

Při nasazování prostředků určíte, že nasazení je přírůstková aktualizace nebo úplná aktualizace. Rozdíl mezi těmito dvěma režimy je způsob, jakým Správce prostředků zpracovává existující prostředky ve skupině prostředků, které nejsou v šabloně.

Pro oba režimy resource manager se pokusí vytvořit všechny prostředky zadané v šabloně. Pokud prostředek již ve skupině prostředků existuje a jeho nastavení se nezmění, nebude pro tento prostředek provedena žádná operace. Pokud změníte hodnoty vlastností prostředku, prostředek se aktualizuje s těmito novými hodnotami. Pokud se pokusíte aktualizovat umístění nebo typ existujícího prostředku, nasazení se nezdaří s chybou. Místo toho nasaďte nový prostředek s umístěním nebo typem, který potřebujete.

Výchozí režim je přírůstkový.

## <a name="complete-mode"></a>Kompletní režim

V úplném režimu Správce prostředků **odstraní** prostředky, které existují ve skupině prostředků, ale nejsou zadány v šabloně.

Pokud vaše šablona obsahuje prostředek, který není nasazen, protože [podmínka](conditional-resource-deployment.md) vyhodnotí na false, výsledek závisí na verzi rozhraní REST API, kterou používáte k nasazení šablony. Pokud používáte verzi starší než 2019-05-10, prostředek **se neodstraní**. S 2019-05-10 nebo novější, prostředek **je odstraněn**. Nejnovější verze Azure PowerShellu a Azure CLI odstraní prostředek.

Buďte opatrní při použití kompletního režimu s [kopírovacími smyčkami](copy-resources.md). Všechny prostředky, které nejsou zadány v šabloně po vyřešení smyčky kopírování, budou odstraněny.

Pokud nasadíte do [více než jedné skupiny prostředků v šabloně](cross-resource-group-deployment.md), prostředky ve skupině prostředků zadané v operaci nasazení jsou způsobilé k odstranění. Prostředky v sekundárních skupinách prostředků se neodstraní.

Existují určité rozdíly v tom, jak typy prostředků zpracovávají odstranění úplného režimu. Nadřazené prostředky se automaticky odstraní, když nejsou v šabloně, která je nasazená v úplném režimu. Některé podřízené prostředky nejsou automaticky odstraněny, pokud nejsou v šabloně. Tyto podřízené prostředky jsou však odstraněny, pokud je odstraněn nadřazený prostředek.

Pokud například vaše skupina prostředků obsahuje zónu DNS (typ prostředku Microsoft.Network/dnsZones) a záznam CNAME (typ prostředku Microsoft.Network/dnsZones/CNAME), je zóna DNS nadřazeným zdrojem záznamu CNAME. Pokud nasadíte v režimu dokončení a nezahrnete zónu DNS do šablony, bude odstraněna zóna DNS i záznam CNAME. Pokud do šablony zahrnete zónu DNS, ale nezahrnete záznam CNAME, cname se neodstraní.

Seznam typů prostředků zpracování odstranění, najdete [v tématu odstranění prostředků Azure pro nasazení v režimu dokončení](complete-mode-deletion.md).

Pokud je skupina prostředků [uzamčena](../management/lock-resources.md), režim dokončení neodstraní prostředky.

> [!NOTE]
> Režim úplného nasazení podporují pouze šablony kořenové úrovně. Pro [propojené nebo vnořené šablony](linked-templates.md)je nutné použít přírůstkový režim.
>
> [Nasazení na úrovni předplatného](deploy-to-subscription.md) nepodporují úplný režim.
>
> V současné době portál nepodporuje úplný režim.
>

## <a name="incremental-mode"></a>Přírůstkový režim

V přírůstkovém režimu správce prostředků **ponechá nezměněné** prostředky, které existují ve skupině prostředků, ale nejsou zadány v šabloně. Prostředky v šabloně **jsou přidány** do skupiny prostředků.

> [!NOTE]
> Při opětovném nasazení existujícího prostředku v přírůstkovém režimu jsou znovu použity všechny vlastnosti. **Vlastnosti nejsou postupně přidány**. Častým nedorozuměním je myslet si, že vlastnosti, které nejsou zadány v šabloně, zůstanou nezměněny. Pokud nezadáte určité vlastnosti, Správce prostředků interpretuje nasazení jako přepsání těchto hodnot. Vlastnosti, které nejsou zahrnuty v šabloně, se obnoví na výchozí hodnoty. Zadejte všechny jiné než výchozí hodnoty pro prostředek, nikoli pouze ty, které aktualizujete. Definice prostředku v šabloně vždy obsahuje konečný stav prostředku. Nemůže představovat částečnou aktualizaci existujícího prostředku.

## <a name="example-result"></a>Příklad výsledku

Chcete-li ilustrovat rozdíl mezi přírůstkové a úplné režimy, zvažte následující scénář.

**Skupina prostředků** obsahuje:

* Zdroj A
* Zdroj B
* Zdroj C

**Šablona** obsahuje:

* Zdroj A
* Zdroj B
* Zdroj D

Při nasazení v **přírůstkovém** režimu má skupina prostředků:

* Zdroj A
* Zdroj B
* Zdroj C
* Zdroj D

Při nasazení v **režimu dokončení** je odstraněn prostředek C. Skupina prostředků má:

* Zdroj A
* Zdroj B
* Zdroj D

## <a name="set-deployment-mode"></a>Nastavení režimu nasazení

Chcete-li nastavit režim nasazení při nasazování pomocí prostředí PowerShell, použijte `Mode` parametr.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Chcete-li nastavit režim nasazení při nasazování `mode` pomocí rozhraní příkazového řádku Azure, použijte parametr.

```azurecli-interactive
az deployment group create \
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

* Další informace o vytváření šablon Správce prostředků najdete v [tématu Vytváření šablon Azure Resource Manageru](template-syntax.md).
* Informace o nasazení prostředků najdete [v tématu Nasazení aplikace pomocí šablony Azure Resource Manager](deploy-powershell.md).
* Informace o operacích pro zprostředkovatele prostředků najdete v [tématu Rozhraní API Azure REST](/rest/api/).
