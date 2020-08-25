---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006411"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavení, resetování nebo zastavení relace

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()` . Tím dojde k zastavení všech sledovacích procesů a zpracování prostředí, a to i v případě, že vyvoláte `ProcessFrame()` . Pak se můžete vyvolávat `Start()` pro obnovení zpracování. Při obnovení se udržují data prostředí, která jsou už zachycená v relaci.
