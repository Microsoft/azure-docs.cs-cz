---
title: Kopírování nebo klonování datové továrny v Azure Data Factory
description: Naučte se kopírovat nebo klonovat datovou továrnu v Azure Data Factory
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
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414108"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopírování nebo klonování datové továrny v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak zkopírovat nebo klonovat datovou továrnu v Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Případy použití při klonování objektu pro vytváření dat

Tady jsou některé okolnosti, za kterých může být užitečné zkopírovat nebo klonovat datovou továrnu:

-   **Přejmenování prostředků**. Azure nepodporuje přejmenování prostředků. Pokud chcete objekt pro vytváření dat přejmenovat, můžete objekt pro vytváření dat naklonovat s jiným názvem a pak odstranit stávající.

-   **Ladění změn** , když funkce ladění nestačí. V některých případech můžete chtít změny otestovat v jiném továrně, než je použijete na svou hlavní. Ve většině scénářů můžete použít ladění. Změny v aktivačních událostech, například jak se chovají změny při automatickém vyvolání triggeru nebo v časovém intervalu, nemusí být testovatelné snadno bez vrácení se změnami. V těchto případech se klonuje továrna a uplatní se vaše změny a přináší spoustu smyslů. Vzhledem k tomu, že se Azure Data Factory poplatky primárně podle počtu spuštění, druhá továrna nevede k žádným dalším poplatkům.

## <a name="how-to-clone-a-data-factory"></a>Jak klonovat datovou továrnu

1. Uživatelské rozhraní Data Factory v Azure Portal umožňuje exportovat celou datovou část datové továrny do šablony Správce prostředků spolu se souborem parametrů, který vám umožní změnit hodnoty, které chcete změnit při klonování objektu pro vytváření.

1. Jako součást je potřeba vytvořit cílovou datovou továrnu z Azure Portal.

1. Pokud máte ve zdrojové továrně SelfHosted IntegrationRuntime, musíte ho v cílové továrně vytvořit se stejným názvem. Pokud chcete sdílet SelfHosted finanční úřad mezi různými továrnami, můžete použít vzor publikovaný [zde](source-control.md#best-practices-for-git-integration).

1. Pokud jste v režimu GIT, pokaždé, když publikujete z portálu, se šablona Správce prostředků továrny uloží do GITU ve větvi adf_publish úložiště.

1. Pro jiné scénáře můžete šablonu Správce prostředků stáhnout kliknutím na tlačítko **Exportovat šablonu správce prostředků** na portálu.

1. Po stažení šablony Správce prostředků ji můžete nasadit pomocí standardních metod nasazení Správce prostředků Template.

1. Z bezpečnostních důvodů vygenerovaná Správce prostředků šablona neobsahuje žádné tajné informace, jako jsou hesla pro propojené služby. V důsledku toho je nutné zadat tato hesla jako parametry nasazení. Pokud zadání parametrů není žádoucí, musíte získat připojovací řetězce a hesla propojených služeb od Azure Key Vault.

## <a name="next-steps"></a>Další kroky

Přečtěte si pokyny k vytvoření datové továrny v Azure Portal v části [Vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](quickstart-create-data-factory-portal.md).
