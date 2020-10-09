---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174720"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

Chcete-li aktualizovat vlastnosti kotvy, použijte `UpdateAnchorProperties()` metodu. Pokud se dvě nebo více zařízení pokusí aktualizovat vlastnosti pro stejnou kotvu ve stejnou dobu, používáme optimistický model souběžnosti. To znamená, že první zápis se načte.  Všem ostatním zápisům se zobrazí chyba "Concurrency" (chyba "Concurrency"): před opakováním pokusu je potřeba aktualizovat vlastnosti.
