---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110553"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

K aktualizaci vlastností anchor, je použít `UpdateAnchorProperties()` metody. Pokud dva nebo více zařízení. Zkuste aktualizovat vlastnosti pro stejné kotvě ve stejnou dobu, můžeme použít model optimistického řízení souběžnosti. To znamená, že prvním zápisu vyhraje.  Všechny ostatní operace zápisu se zobrazí chyba "Souběžnosti": aktualizace vlastností bylo by potřeba provést před dalším pokusem.
