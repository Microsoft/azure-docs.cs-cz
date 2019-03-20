---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907636"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

Pokud chcete aktualizovat vlastnosti anchor, použijete metodu UpdateAnchorProperties. Pokud dva nebo více zařízení. Zkuste aktualizovat vlastnosti pro stejné kotvě ve stejnou dobu, můžeme použít model optimistického řízení souběžnosti. To znamená, že prvním zápisu vyhraje.  Všechny ostatní operace zápisu se zobrazí chyba "Souběžnosti": aktualizace vlastností bylo by potřeba provést před dalším pokusem.
