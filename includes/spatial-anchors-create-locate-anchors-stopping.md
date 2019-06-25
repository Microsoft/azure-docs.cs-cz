---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174728"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavit, obnovit nebo zastavte relaci.

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()`. Tím se zastaví všechny sledovací procesy a zpracování prostředí i v případě vyvolání ProcessFrame(). Potom můžete vyvolat `Start()` obnovit zpracování. Při obnovení dat prostředí zaznamenali během relace se zachová.
