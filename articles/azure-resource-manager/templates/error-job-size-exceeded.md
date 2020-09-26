---
title: Chyba překročení velikosti úlohy
description: Popisuje, jak řešit chyby, pokud je velikost nebo Šablona úlohy příliš velká.
ms.topic: troubleshooting
ms.date: 09/25/2020
ms.openlocfilehash: 06645561964d9634d93061b3be4d100a578cc7e7
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91373115"
---
# <a name="resolve-errors-for-job-size-exceeded"></a>Řešení chyb pro překročení velikosti úlohy

Tento článek popisuje, jak vyřešit chyby **JobSizeExceededException** a **DeploymentSizeExceededException** .

## <a name="symptom"></a>Příznak

Při nasazování šablony se zobrazí chyba s oznámením, že nasazení překročilo limity.

## <a name="cause"></a>Příčina

Tato chyba se může zobrazit, když velikost šablony překročí 4 MB. Limit velikosti 4 MB se vztahuje na konečný stav šablony po rozbalení pro definice prostředků, které používají [kopírování](copy-resources.md) k vytvoření mnoha instancí. Konečný stav zahrnuje také vyřešené hodnoty pro proměnné a parametry.

Úloha nasazení zahrnuje i metadata o žádosti. U velkých šablon může metadata v kombinaci s šablonou překročit povolenou velikost úlohy.

Další omezení pro tuto šablonu:

* parametry 256
* proměnné 256
* 800 prostředků (včetně počtu kopií)
* 64 výstupní hodnoty
* 24 576 znaků ve výrazu šablony

## <a name="solution-1---simplify-template"></a>Řešení 1 – zjednodušení šablony

První možností je zjednodušit šablonu. Tato možnost funguje, když šablona nasadí spoustu různých typů prostředků. Zvažte možnost rozdělit šablonu do [propojených šablon](linked-templates.md). Rozdělte typy prostředků do logických skupin a přidejte propojenou šablonu pro každou skupinu. Pokud například potřebujete nasadit spoustu síťových prostředků, můžete tyto prostředky přesunout do propojené šablony.

Další prostředky můžete nastavit jako závislé na propojené šabloně a [získat hodnoty z výstupu propojené šablony](linked-templates.md#get-values-from-linked-template).

## <a name="solution-2---use-serial-copy"></a>Řešení 2 – použití sériového kopírování

Druhou možností je změnit smyčku kopírování z [paralelního zpracování na sériové](copy-resources.md#serial-or-parallel). Tuto možnost použijte jenom v případě, že se domníváte, že při nasazování velkého počtu prostředků přes kopii dojde k chybě. Tato změna může významně prodloužit dobu nasazení, protože prostředky nejsou nasazeny paralelně.
