---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174720"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

K aktualizaci vlastností anchor, je použít `UpdateAnchorProperties()` metody. Pokud dva nebo více zařízení. Zkuste aktualizovat vlastnosti pro stejné kotvě ve stejnou dobu, můžeme použít model optimistického řízení souběžnosti. To znamená, že prvním zápisu vyhraje.  Všechny ostatní operace zápisu se zobrazí chyba "Souběžnosti": aktualizace vlastností bylo by potřeba provést před dalším pokusem.
