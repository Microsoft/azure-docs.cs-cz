---
title: Chyb neplatných šablon Azure | Dokumentace Microsoftu
description: Popisuje postup řešení chyb neplatných šablon.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: ee4bce38e0fcde93ba0417617ae90dab2eefda67
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316196"
---
# <a name="resolve-errors-for-invalid-template"></a>Řešení chyb neplatný šablony

Tento článek popisuje, jak vyřešit chyb neplatných šablon.

## <a name="symptom"></a>Příznak

Při nasazování šablony, se zobrazí chyba s upozorněním:

```
Code=InvalidTemplate
Message=<varies>
```

Chybová zpráva závisí na typu chyby.

## <a name="cause"></a>Příčina

Tato chyba může být následek několik různých typů chyb. Obvykle zahrnují syntaxe nebo strukturální Chyba v šabloně.

<a id="syntax-error" />

## <a name="solution-1---syntax-error"></a>Řešení 1 - Chyba syntaxe

Pokud se zobrazí chybová zpráva, která určuje ověření šablony se nezdařilo, možná syntaxe ve vaší šabloně.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Tato chyba je snadné vytvořit, protože výrazy šablony může být složité. Například následující přiřazení název účtu úložiště má jednu sadu závorek, tři funkce, tří párů závorek, jednu sadu jednoduchých uvozovek a jednu vlastnost:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Pokud nezadáte syntaxe porovnávání, šablona vytváří hodnotu, která se liší od váš záměr.

Když dostanete tento typ chyby, pečlivě si prostudujte syntaxe výrazu. Zvažte použití editoru JSON, jako je [sady Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) nebo [Visual Studio Code](resource-manager-vs-code.md), která vás upozorní chyby syntaxe.

<a id="incorrect-segment-lengths" />

## <a name="solution-2---incorrect-segment-lengths"></a>Řešení 2 – nesprávný segment délky

Další Chyba Neplatná šablona nastane, pokud název prostředku není ve správném formátu.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

Kořenové úrovně prostředku musí mít jeden menší segment v názvu než v typu prostředku. Každý segment je rozlišené pomocí lomítkem. V následujícím příkladu typ má dva segmenty a název obsahuje jeden segment, takže má **platný název**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Je další příklad, ale **není platný název** protože má stejný počet segmentů jako typ.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

Pro podřízené prostředky typ a název mít stejný počet segmentů. Tento počet segmentů dává smysl, protože úplný název a typ pro podřízené zahrnuje nadřazené názvem a typem. Úplný název proto má stále jeden menší segment než úplným typem.

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

Získání správné segmenty může být velmi obtížné s typy Resource Manageru, které se použilo pro poskytovatele prostředků. Například použití zámek prostředku na webu vyžaduje typ s čtyři segmenty. Název je proto tři segmenty:

```json
{
    "type": "Microsoft.Web/sites/providers/locks",
    "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
    ...
}
```

<a id="parameter-not-valid" />

## <a name="solution-3---parameter-is-not-valid"></a>Řešení 3 – parametr není platný

Pokud zadáte hodnotu parametru, jež není jednou z povolených hodnot, zobrazí se zpráva podobná následující chybu:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Double zkontrolujte povolené hodnoty v šabloně a zadejte jednu během nasazení. Další informace o povolených parametrů najdete v tématu [oddílu parametry šablon Azure Resource Manageru](resource-group-authoring-templates.md#parameters).

<a id="too-many-resource-groups" />

## <a name="solution-4---too-many-target-resource-groups"></a>Řešení 4 – příliš mnoho skupin prostředků cíle

Pokud chcete zadat víc než pět skupin prostředků cíle v jednom nasazení, tato chyba se zobrazí. Zvažte sloučení počet skupin prostředků ve vašem nasazení nebo nasazení některé šablony jako samostatná nasazení. Další informace najdete v tématu [Azure nasadit prostředky do více než jedno předplatné nebo skupinu prostředků](resource-manager-cross-resource-group-deployment.md).

<a id="circular-dependency" />

## <a name="solution-5---circular-dependency-detected"></a>Řešení 5 – cyklická závislost zjistil

Tato chyba se zobrazí, když prostředky jsou vzájemně závislé způsobem, který zabrání nasazení spustit. Díky kombinaci vzájemné závislosti dva nebo více prostředků, počkejte další prostředky, které jsou také čekání. Například zdroj1 závisí na resource3 zdroj2 závisí na zdroj1 a resource3 závisí na zdroj2. Obvykle můžete tento problém vyřešit odstraněním nepotřebných závislosti.

Chcete-li vyřešit cyklická závislost:

1. V šabloně najdete prostředek určený v cyklická závislost. 
2. Pro daný prostředek, zkontrolujte **dependsOn** vlastnost a při každém použití **odkaz** funkce na prostředcích, které závisí. 
3. Prozkoumejte tyto prostředky na prostředcích, které jsou závislé. Postupujte podle závislostí, dokud si všimnete prostředek, který závisí na původní zdroj.
5. Pro prostředky, které jsou součástí cyklické závislosti, pečlivě zkontrolujte všechna použití **dependsOn** vlastnost k identifikaci případných závislostí, které nejsou potřebné. Odebrání těchto závislostí. Pokud si nejste jistí, že je potřeba závislý, odstraňte ho. 
6. Znovu nasaďte šablonu.

Odebrání hodnoty z **dependsOn** vlastnost mohou způsobit chyby při nasazování šablony. Pokud dojde k chybě, přidejte závislosti zpět do šablony. 

Pokud tento přístup nevyřeší cyklická závislost, zvažte přesunutí části logiky nasazení do podřízených prostředků (jako je například rozšíření nebo nastavení konfigurace). Nakonfigurujte tyto podřízené prostředky k nasazení po prostředky zahrnuté v cyklická závislost. Předpokládejme například, že nasazujete dva virtuální počítače, ale musíte zadat vlastnosti na každé z nich odkazovat na druhý. Můžete je nasadit v následujícím pořadí:

1. vm1
2. vm2
3. Rozšíření na vm1 závisí na vm1 a vm2. Rozšíření nastaví hodnoty pro vm1, který získá z vm2.
4. Rozšíření na vm2 závisí na vm1 a vm2. Rozšíření nastaví hodnoty pro vm2, který získá z vm1.

Stejný postup funguje u aplikací služby App Service. Zvažte přesunutí hodnoty konfigurace do podřízených prostředků sady prostředků aplikace. Můžete nasadit dvě webové aplikace v následujícím pořadí:

1. webapp1
2. webapp2
3. konfigurace pro webapp1 závisí na webapp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z webapp2.
4. konfigurace pro webapp2 závisí na webapp1 a webapp2. Obsahuje nastavení aplikace s hodnotami z webapp1.
