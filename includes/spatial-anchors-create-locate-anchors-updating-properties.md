---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "67174720"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

Chcete-li aktualizovat vlastnosti kotvy, použijte `UpdateAnchorProperties()` metodu. Pokud se dvě nebo více zařízení pokusí aktualizovat vlastnosti pro stejnou kotvu ve stejnou dobu, používáme optimistický model souběžnosti. To znamená, že první zápis se načte.  Všem ostatním zápisům se zobrazí chyba "Concurrency" (chyba "Concurrency"): před opakováním pokusu je potřeba aktualizovat vlastnosti.
