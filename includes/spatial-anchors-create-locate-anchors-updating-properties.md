---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632087"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

K aktualizaci vlastností anchor, je použít `UpdateAnchorProperties()` metody. Pokud dva nebo více zařízení. Zkuste aktualizovat vlastnosti pro stejné kotvě ve stejnou dobu, můžeme použít model optimistického řízení souběžnosti. To znamená, že prvním zápisu vyhraje.  Všechny ostatní operace zápisu se zobrazí chyba "Souběžnosti": aktualizace vlastností bylo by potřeba provést před dalším pokusem.
