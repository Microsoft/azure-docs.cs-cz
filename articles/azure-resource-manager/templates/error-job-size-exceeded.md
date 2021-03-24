---
title: Chyba překročení velikosti úlohy
description: Popisuje, jak řešit chyby, pokud je velikost nebo Šablona úlohy příliš velká.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b39a0bba15e73bab1a85cbd9e36efebf82d6cf42
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889361"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Řešení chyb pro překročení velikosti úlohy

Tento článek popisuje, jak vyřešit chyby **JobSizeExceededException** a **DeploymentJobSizeExceededException** .

## <a name="symptom"></a>Příznak

Při nasazování šablony se zobrazí chyba s oznámením, že nasazení překročilo limity.

## <a name="cause"></a>Příčina

Tato chyba se zobrazí, pokud nasazení překročí jedno z povolených limitů. Obvykle se tato chyba zobrazí, pokud buď šablona nebo úloha, která spouští nasazení, je příliš velká.

Úloha nasazení nemůže být větší než 1 MB. Úloha zahrnuje metadata o žádosti. U velkých šablon můžou metadata v kombinaci s šablonou překročit povolenou velikost pro úlohy.

Šablona nemůže být větší než 4 MB. Limit velikosti 4 MB se vztahuje na konečný stav šablony po rozbalení pro definice prostředků, které používají [kopírování](copy-resources.md) k vytvoření mnoha instancí. Konečný stav zahrnuje také přeložené hodnoty proměnných a parametrů.

Další omezení pro tuto šablonu:

* parametry 256
* proměnné 256
* 800 prostředků (včetně počtu kopií)
* 64 výstupní hodnoty
* 24 576 znaků ve výrazu šablony

Při použití kopírovacích smyček k nasazení prostředku Nepoužívejte název smyčky jako závislost:

```json
dependsOn: [ "nicLoop" ]
```

Místo toho použijte instanci prostředku ze smyčky, na které je třeba závislé. Například:

```json
dependsOn: [
    "[resourceId('Microsoft.Network/networkInterfaces', concat('nic-', copyIndex()))]"
]
```

## <a name="solution-1---simplify-template"></a>Řešení 1 – zjednodušení šablony

První možností je zjednodušit šablonu. Tato možnost funguje, když šablona nasadí spoustu různých typů prostředků. Zvažte možnost rozdělit šablonu do [propojených šablon](linked-templates.md). Rozdělte typy prostředků do logických skupin a přidejte propojenou šablonu pro každou skupinu. Pokud například potřebujete nasadit spoustu síťových prostředků, můžete tyto prostředky přesunout do propojené šablony.

Další prostředky můžete nastavit jako závislé na propojené šabloně a [získat hodnoty z výstupu propojené šablony](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---reduce-name-size"></a>Řešení 2 – zmenšení velikosti názvu

Zkuste zkrátit délku názvů, které používáte pro [parametry](template-parameters.md), [proměnné](template-variables.md)a [výstupy](template-outputs.md). Pokud jsou tyto hodnoty opakovány prostřednictvím smyčky kopírování, velký název se vynásobí mnohokrát.