---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694469"
---
## <a name="provide-feedback-to-the-user"></a>Poskytněte uživateli zpětnou vazbu.

Můžete napsat kód pro zpracování události aktualizace relace. Tato událost se aktivuje pokaždé, když relace zlepšuje své porozumění vašemu okolí. V takovém případě vám umožní:

- Použijte `UserFeedback` třídu k poskytnutí zpětné vazby uživateli při přesunu zařízení a relace aktualizuje porozumění prostředí. K tomu je potřeba
- Určete, v jakém bodě je k dispozici dostatek sledovaných prostorových dat pro vytvoření prostorových ukotvení. Určíte ho buď pomocí, `ReadyForCreateProgress` nebo `RecommendedForCreateProgress` . Jakmile `ReadyForCreateProgress` je výše 1, máme dostatek dat pro uložení cloudové kotvy, ale doporučujeme počkat na to, než `RecommendedForCreateProgress` 1.
