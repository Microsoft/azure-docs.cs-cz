---
title: Neplatné chyby šablony
description: Popisuje, jak vyřešit neplatné chyby šablony při nasazování šablon Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.openlocfilehash: 65cd69d67933d117b51f37b587b276aec2bd635a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154053"
---
# <a name="resolve-errors-for-invalid-template"></a>Řešení chyb kvůli neplatné šabloně

Tento článek popisuje, jak vyřešit neplatné chyby šablony.

## <a name="symptom"></a>Příznak

Při nasazování šablony se zobrazí chyba označující:

```
Code=InvalidTemplate
Message=<varies>
```

Chybová zpráva závisí na typu chyby.

## <a name="cause"></a>Příčina

Tato chyba může být důsledkem několika různých typů chyb. Obvykle zahrnují syntaxi nebo strukturální chybu v šabloně.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Řešení 1 - syntaktická chyba

Pokud se zobrazí chybová zpráva, která označuje, že se ověření šablony nezdařilo, může se v šabloně zobrazit problém se syntaxí.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Tuto chybu lze snadno provést, protože výrazy šablony mohou být složité. Například následující přiřazení názvu pro účet úložiště má jednu sadu závorek, tři funkce, tři sady závorek, jednu sadu jednoduchých uvozovek a jednu vlastnost:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Pokud nezadáte odpovídající syntaxi, šablona vytvoří hodnotu, která se liší od vašeho záměru.

Když se zobrazí tento typ chyby, pečlivě zkontrolujte syntaxi výrazu. Zvažte použití editoru JSON, jako je [Visual Studio](create-visual-studio-deployment-project.md) nebo Visual [Studio Code](use-vs-code-to-create-template.md), který vás může varovat před chybami syntaxe.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Řešení 2 - nesprávné délky segmentů

Další neplatná chyba šablony nastane, když název prostředku není ve správném formátu.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Prostředek kořenové úrovně musí mít v názvu o jeden segment méně než v typu prostředku. Každý segment je rozlišen lomítkem. V následujícím příkladu má typ dva segmenty a název má jeden segment, takže se jedná o **platný název**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Ale další příklad **není platný název,** protože má stejný počet segmentů jako typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Pro podřízené prostředky mají typ a název stejný počet segmentů. Tento počet segmentů má smysl, protože celé jméno a typ podřízeného zahrnuje nadřazený název a typ. Úplný název má proto stále o jeden segment menší než úplný typ.

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

Správné získání segmentů může být složité s typy Správce prostředků, které jsou použity napříč poskytovateli prostředků. Použití zámku prostředků na webu například vyžaduje typ se čtyřmi segmenty. Proto je název tři segmenty:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Řešení 3 - parametr není platný

Pokud zadáte hodnotu parametru, která není jednou z povolených hodnot, zobrazí se zpráva podobná následující chybě:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Zkontrolujte povolené hodnoty v šabloně a zadejte jeden během nasazení. Další informace o povolených hodnotách parametrů najdete [v části Parametry v šablonách Azure Resource Manageru](template-syntax.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Řešení 4 – příliš mnoho cílových skupin prostředků

Pokud zadáte více než pět cílových skupin prostředků v jednom nasazení, zobrazí se tato chyba. Zvažte buď konsolidaci počtu skupin prostředků ve vašem nasazení, nebo nasazení některých šablon jako samostatných nasazení. Další informace najdete [v tématu Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků](cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Řešení 5 – zjištěna cyklická závislost

Tato chyba se zobrazí, když prostředky závisí na sobě způsobem, který zabraňuje spuštění nasazení. Kombinace vzájemné závislosti způsobí, že dva nebo více prostředků čekat na jiné prostředky, které jsou také čekání. Například resource1 závisí na resource3, resource2 závisí na resource1 a resource3 závisí na resource2. Tento problém můžete obvykle vyřešit odebráním zbytečných závislostí.

Řešení cyklické závislosti:

1. V šabloně vyhledejte prostředek identifikovaný v cyklické závislosti.
2. Pro tento prostředek zkontrolujte **dependsOn** vlastnost a všechna použití **referenční** funkce zobrazíte, které prostředky závisí na.
3. Zkontrolujte tyto prostředky a zjistěte, na kterých prostředcích jsou závislé. Postupujte podle závislostí, dokud si nevšimnete prostředku, který závisí na původním prostředku.
5. Pro prostředky zapojené do cyklické závislosti pečlivě zkontrolujte všechna použití **dependsOn** vlastnost k identifikaci všechny závislosti, které nejsou potřebné. Odeberte tyto závislosti. Pokud si nejste jisti, že je zapotřebí závislost, zkuste ji odebrat.
6. Znovu nasadit šablonu.

Odebrání hodnot z **vlastnosti dependsOn** může způsobit chyby při nasazení šablony. Pokud se zobrazí chyba, přidejte závislost zpět do šablony.

Pokud tento přístup nevyřeší cyklické závislosti, zvažte přesunutí části logiky nasazení do podřízených prostředků (například rozšíření nebo nastavení konfigurace). Nakonfigurujte tyto podřízené prostředky k nasazení po prostředky zapojené do cyklické závislosti. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte nastavit vlastnosti na každém z nich, které odkazují na druhé. Můžete je nasadit v následujícím pořadí:

1. vm1
2. vm2
3. Rozšíření na vm1 závisí na vm1 a vm2. Rozšíření nastaví hodnoty na vm1, které získá z vm2.
4. Rozšíření na vm2 závisí na vm1 a vm2. Rozšíření nastaví hodnoty na vm2, které získá z vm1.

Stejný přístup funguje pro aplikace App Service. Zvažte přesunutí hodnot konfigurace do podřízeného prostředku prostředku aplikace. Můžete nasadit dvě webové aplikace v následujícím pořadí:

1. webová aplikace1
2. webová aplikace2
3. config pro webapp1 závisí na webapp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z webapp2.
4. config pro webapp2 závisí na webapp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z webapp1.
