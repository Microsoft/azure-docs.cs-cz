---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67174728"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavení, obnovení nebo ukončení relace

Chcete-li relaci dočasně zastavit, můžete vyvolat `Stop()`. Tím se zastaví všechny sledovací procesy a zpracování prostředí, i v případě, že vyvolat ProcessFrame(). Potom můžete `Start()` vyvolat pokračovat ve zpracování. Při obnovení jsou zachována data prostředí, která jsou již v relaci zachycena.
