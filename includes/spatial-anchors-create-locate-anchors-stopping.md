---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232598"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavit, obnovit nebo zastavte relaci.

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()`. Tím se zastaví všechny sledovací procesy a zpracování prostředí i v případě vyvolání ProcessFrame(). Potom můžete vyvolat `Start()` obnovit zpracování. Při obnovení dat prostředí zaznamenali během relace se zachová.
