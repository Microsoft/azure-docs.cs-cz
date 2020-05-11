---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006411"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavení, resetování nebo zastavení relace

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()`. Tím dojde k zastavení všech sledovacích procesů a zpracování prostředí, a to i `ProcessFrame()`v případě, že vyvoláte. Pak se můžete vyvolávat `Start()` pro obnovení zpracování. Při obnovení se udržují data prostředí, která jsou už zachycená v relaci.
