---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694469"
---
## <a name="provide-feedback-to-the-user"></a>Poskytněte uživateli zpětnou vazbu.

Můžete napsat kód pro zpracování události aktualizace relace. Tato událost se aktivuje pokaždé, když relace zlepšuje své porozumění vašemu okolí. V takovém případě vám umožní:

- Použijte třídu `UserFeedback` k poskytnutí zpětné vazby uživateli při přesunu zařízení a relace aktualizuje porozumění prostředí. K tomu je potřeba
- Určete, v jakém bodě je k dispozici dostatek sledovaných prostorových dat pro vytvoření prostorových ukotvení. Určíte ho buď pomocí `ReadyForCreateProgress`, nebo `RecommendedForCreateProgress`. Jakmile je `ReadyForCreateProgress` větší než 1, máme dostatek dat pro uložení cloudové kotvy, ale doporučujeme počkat na to, až `RecommendedForCreateProgress` nad 1.
