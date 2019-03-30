---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632201"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavit, obnovit nebo zastavte relaci.

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()`. Tím se zastaví všechny sledovací procesy a zpracování prostředí i v případě vyvolání ProcessFrame(). Potom můžete vyvolat `Start()` obnovit zpracování. Při obnovení dat prostředí zaznamenali během relace se zachová.
