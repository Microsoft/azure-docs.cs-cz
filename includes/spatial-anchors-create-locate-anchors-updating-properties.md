---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67174720"
---
## <a name="update-properties"></a>Aktualizovat vlastnosti

Chcete-li aktualizovat vlastnosti kotvy, použijte metodu. `UpdateAnchorProperties()` Pokud dvě nebo více zařízení se pokusí aktualizovat vlastnosti pro stejnou kotvu ve stejnou dobu, použijeme optimistický model souběžnosti. Což znamená, že první zápis vyhraje.  Všechny ostatní zápisy se zobrazí chyba "Souběžnost": aktualizace vlastností by bylo zapotřebí před pokusem znovu.
