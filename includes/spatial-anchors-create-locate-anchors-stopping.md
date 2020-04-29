---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174728"
---
## <a name="pause-reset-or-stop-the-session"></a>Pozastavení, resetování nebo zastavení relace

Chcete-li ukončit relaci dočasně, můžete vyvolat `Stop()`. Tím dojde k zastavení všech sledovacích procesů a zpracování prostředí, a to i v případě, že jste vyvolali ProcessFrame (). Pak se můžete vyvolávat `Start()` pro obnovení zpracování. Při obnovení se udržují data prostředí, která jsou už zachycená v relaci.
