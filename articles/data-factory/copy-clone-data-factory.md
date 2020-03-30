---
title: Kopírování nebo klonování datové továrny ve Službě Azure Data Factory
description: Přečtěte si, jak zkopírovat nebo klonovat datovou továrnu v Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: bafe70655f05b5dda32f51393591f82b4b5625f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73678133"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopírování nebo klonování datové továrny ve Službě Azure Data Factory

Tento článek popisuje, jak zkopírovat nebo klonovat datové továrny v Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Případy použití pro klonování datové továrny

Zde jsou některé z okolností, za kterých může být užitečné kopírovat nebo klonovat data factory:

-   **Přejmenování prostředků**. Azure nepodporuje přejmenování prostředků. Pokud chcete přejmenovat továrnu dat, můžete klonovat data factory s jiným názvem a potom odstranit existující.

-   **Ladění se změní,** když funkce ladění nejsou dostatečné. Někdy chcete-li otestovat změny, můžete otestovat změny v jiné továrně před jejich použitím na hlavní. Ve většině scénářů můžete použít ladění. Změny v aktivačních událostech, například jak se změny chovají při automatickém vyvolání aktivační události nebo v časovém okně, však nemusí být snadno testovatelné bez vrácení se změnami. V těchto případech klonování továrny a použití změn tam dává velký smysl. Vzhledem k tomu, že Azure Data Factory poplatky především podle počtu spuštění, druhá továrna nevede k žádné další poplatky.

## <a name="how-to-clone-a-data-factory"></a>Jak klonovat datovou továrnu

1. Uživatelské rozhraní datové továrny na portálu Azure umožňuje exportovat celou datovou část vaší datové továrny do šablony Správce prostředků spolu se souborem parametrů, který umožňuje změnit všechny hodnoty, které chcete změnit při klonování vaší továrny.

1. Jako předpoklad je potřeba vytvořit cílovou datovou továrnu z webu Azure Portal.

1. Pokud máte SelfHosted IntegrationRuntime ve zdrojové továrně, musíte jej předem vytvořit se stejným názvem v cílové továrně. Pokud chcete sdílet SelfHosted IRs mezi různými továrnami, můžete použít vzor publikovaný [zde](source-control.md#best-practices-for-git-integration).

1. Pokud jste v režimu GIT, pokaždé, když publikujete z portálu, šablona Správce prostředků továrny je uložena do GIT v adf_publish větev úložiště.

1. V jiných scénářích lze šablonu Správce prostředků stáhnout kliknutím na tlačítko **Exportovat správce prostředků** na portálu.

1. Po stažení šablony Správce prostředků ji můžete nasadit pomocí standardních metod nasazení šablony Resource Manageru.

1. Z bezpečnostních důvodů neobsahuje vygenerovaná šablona Správce prostředků žádné tajné informace, například hesla pro propojené služby. V důsledku toho je třeba zadat tato hesla jako parametry nasazení. Pokud poskytování parametrů není žádoucí, budete muset získat připojovací řetězce a hesla propojených služeb z Azure Key Vault.

## <a name="next-steps"></a>Další kroky

Projděte si pokyny pro vytvoření datové továrny na webu Azure Portal v [části Vytvoření datové továrny pomocí uhlavního nastavení Azure Data Factory](quickstart-create-data-factory-portal.md).
