---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "67174720"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

Chcete-li aktualizovat vlastnosti kotvy, použijte `UpdateAnchorProperties()` metodu. Pokud se dvě nebo více zařízení pokusí aktualizovat vlastnosti pro stejnou kotvu ve stejnou dobu, používáme optimistický model souběžnosti. To znamená, že první zápis se načte.  Všem ostatním zápisům se zobrazí chyba "Concurrency" (chyba "Concurrency"): před opakováním pokusu je potřeba aktualizovat vlastnosti.
