---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76694469"
---
## <a name="provide-feedback-to-the-user"></a>Poskytnutí zpětné vazby uživateli

Můžete napsat kód pro zpracování aktualizované události relace. Tato událost se spustí pokaždé, když relace zlepší své chápání vašeho okolí. Přitom vám umožní:

- Pomocí `UserFeedback` třídy můžete poskytnout zpětnou vazbu uživateli při pohybu zařízení a relace aktualizuje své chápání prostředí. Chcete-li to provést,
- Určete, v jakém okamžiku je dostatek sledovaných prostorových dat k vytvoření prostorových kotev. Toto určení `ReadyForCreateProgress` určíte buď nebo `RecommendedForCreateProgress`. Jakmile `ReadyForCreateProgress` je vyšší než 1, máme dostatek dat pro uložení prostorové kotvy cloudu, i když doporučujeme počkat, dokud `RecommendedForCreateProgress` není vyšší než 1, aby tak učinily.
