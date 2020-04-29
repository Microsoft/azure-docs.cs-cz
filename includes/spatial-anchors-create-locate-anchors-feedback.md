---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76694469"
---
## <a name="provide-feedback-to-the-user"></a>Poskytněte uživateli zpětnou vazbu.

Můžete napsat kód pro zpracování události aktualizace relace. Tato událost se aktivuje pokaždé, když relace zlepšuje své porozumění vašemu okolí. V takovém případě vám umožní:

- Použijte `UserFeedback` třídu k poskytnutí zpětné vazby uživateli při přesunu zařízení a relace aktualizuje porozumění prostředí. K tomu je potřeba
- Určete, v jakém bodě je k dispozici dostatek sledovaných prostorových dat pro vytvoření prostorových ukotvení. Určíte ho buď `ReadyForCreateProgress` pomocí, `RecommendedForCreateProgress`nebo. Jakmile `ReadyForCreateProgress` je výše 1, máme dostatek dat pro uložení cloudové kotvy, ale doporučujeme počkat na to, než `RecommendedForCreateProgress` 1.
