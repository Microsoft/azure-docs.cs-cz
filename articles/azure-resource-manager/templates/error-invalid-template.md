---
title: Neplatné chyby šablony
description: Popisuje způsob řešení neplatných chyb šablon při nasazení Azure Resource Manager šablon.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96185789"
---
# <a name="resolve-errors-for-invalid-template"></a>Řešení chyb kvůli neplatné šabloně

Tento článek popisuje, jak vyřešit neplatné chyby šablony.

## <a name="symptom"></a>Příznak

Při nasazování šablony se zobrazí chyba s oznámením:

```
Code=InvalidTemplate
Message=<varies>
```

Chybová zpráva závisí na typu chyby.

## <a name="cause"></a>Příčina

Tato chyba může být způsobena několika různými typy chyb. Obvykle obsahují syntaktickou nebo strukturální chybu v šabloně.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Řešení 1 – Chyba syntaxe

Pokud se zobrazí chybová zpráva s oznámením, že se nepodařilo ověřit šablonu, můžete mít v šabloně problém se syntaxí.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Tato chyba se dá snadno vytvořit, protože výrazy šablon můžou být komplikované. Například následující přiřazení názvu účtu úložiště má jednu sadu závorek, tři funkce, tři sady závorek, jednu sadu jednoduchých uvozovek a jednu vlastnost:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Pokud neposkytnete odpovídající syntaxi, šablona vytvoří hodnotu, která se liší od vašeho záměru.

Pokud obdržíte tento typ chyby, pečlivě zkontrolujte syntaxi výrazu. Zvažte použití editoru JSON, jako je například [Visual Studio](create-visual-studio-deployment-project.md) nebo [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), což vám může upozorňovat na chyby syntaxe.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Řešení 2 – nesprávné délky segmentů

Jiná neplatná Chyba šablony nastane, pokud název prostředku není ve správném formátu.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Prostředek kořenové úrovně musí mít jeden míň segment v názvu než v typu prostředku. Každý segment je rozlišen lomítkem. V následujícím příkladu má typ dva segmenty a název má jeden segment, takže se jedná o **platný název**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ale následující příklad není **platný název** , protože má stejný počet segmentů jako typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

U podřízených prostředků má typ a název stejný počet segmentů. Tento počet segmentů dává smysl, protože úplný název a typ podřízeného objektu obsahují nadřazený název a typ. Proto má úplný název stále jeden segment menší než úplný typ.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Napravení segmentů může být obtížné s Správce prostředků typy, které se používají napříč poskytovateli prostředků. Například použití zámku prostředku na webu vyžaduje typ se čtyřmi segmenty. Název je proto tři segmenty:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>Řešení 3 – parametr není platný.

Pokud zadáte hodnotu parametru, která není jednou z povolených hodnot, zobrazí se zpráva podobná následující chybě:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Ověřte, že jsou v šabloně povolené hodnoty, a během nasazování ho zadejte. Další informace o povolených hodnotách parametrů naleznete v [části Parameters of Azure Resource Manager Templates](template-syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Řešení 4 – moc velký počet cílových skupin prostředků

Tato chyba se může zobrazit v dřívějších nasazeních, protože jste v jednom nasazení omezili na pět cílových skupin prostředků. V květnu 2020 se toto omezení zvýšilo na 800 skupin prostředků. Další informace najdete v tématu [nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](./deploy-to-resource-group.md).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Řešení 5 – zjistila se cyklická závislost.

Tato chyba se zobrazí, když na sebe navzájem závisí prostředky, které zabrání spuštění nasazení. Kombinace vzájemných závislostí vytváří u jiných prostředků, které také čekají, dva nebo více zdrojů. Například Resource1 závisí na Resource3, Resource2 závisí na Resource1 a Resource3 závisí na Resource2. Tento problém se obvykle dá vyřešit odebráním zbytečných závislostí.

Postup při řešení kruhové závislosti:

1. V šabloně vyhledejte prostředek identifikovaný v cyklické závislosti.
2. Pro tento prostředek Zkontrolujte vlastnost **dependsOn** a jakékoli použití **referenční** funkce k zobrazení prostředků, na kterých závisí.
3. Prohlédněte si tyto prostředky, abyste viděli, na kterých prostředcích závisí. Sledujte závislosti, dokud si nevšimnete zdroje, který závisí na původním prostředku.
5. U prostředků, které jsou součástí cyklické závislosti, pečlivě prověřte všechna použití vlastnosti **dependsOn** a Identifikujte závislosti, které nepotřebujete. Odeberte tyto závislosti. Pokud si nejste jistí, že je potřeba závislost, zkuste ji odebrat.
6. Znovu nasaďte šablonu.

Odebrání hodnot z vlastnosti **dependsOn** může způsobit chyby při nasazení šablony. Pokud se zobrazí chyba, přidejte závislost zpátky do šablony.

Pokud tento přístup nevyřešil cyklickou závislost, zvažte přesunutí části logiky nasazení do podřízených prostředků (například rozšíření nebo nastavení konfigurace). Tyto podřízené prostředky nakonfigurujte tak, aby se nasadily po zapojení prostředků do cyklické závislosti. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte nastavit vlastnosti pro každý z nich, který odkazuje na druhý. Můžete je nasadit v tomto pořadí:

1. vm1
2. VM2
3. Přípona v VM1 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM1, které získá z VM2.
4. Přípona v VM2 závisí na VM1 a VM2. Rozšíření nastaví hodnoty na VM2, které získá z VM1.

Stejný přístup funguje i pro App Service aplikace. Zvažte přesunutí hodnot konfigurace do podřízeného prostředku prostředku aplikace. Můžete nasadit dvě webové aplikace v uvedeném pořadí:

1. webapp1
2. webapp2
3. konfigurace pro WebApp1 závisí na WebApp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z webapp2.
4. konfigurace pro webapp2 závisí na WebApp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z WebApp1.