---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006411"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavení, resetování nebo zastavení relace

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()` . Tím dojde k zastavení všech sledovacích procesů a zpracování prostředí, a to i v případě, že vyvoláte `ProcessFrame()` . Pak se můžete vyvolávat `Start()` pro obnovení zpracování. Při obnovení se udržují data prostředí, která jsou už zachycená v relaci.
