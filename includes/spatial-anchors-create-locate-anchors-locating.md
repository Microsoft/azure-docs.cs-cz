---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "76887651"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledání prostorové kotvy v cloudu

Možnost najít dříve uloženou cloudovou prostorovou kotvu je jedním z hlavních důvodů pro použití prostorových kotvy Azure. Existuje několik různých způsobů, jak můžete najít prostorovou kotvu v cloudu. Můžete použít jednu strategii na sledovací proces najednou.
- Vyhledejte kotvy podle identifikátoru.
- Vyhledejte kotvy připojené k dříve umístěné kotvě. Můžete se dozvědět o kotvy vztahy [zde](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/).
- Vyhledejte kotvu pomocí [hrubé relokalizace](/azure/spatial-anchors/concepts/coarse-reloc/).

Pokud vyhledáváte cloudové prostorové kotvy podle identifikátoru, budete chtít uložit identifikátor prostorové kotvy cloudu do back-endové služby vaší aplikace a zpřístupnit ho všem zařízením, která se na něm můžou správně ověřit. Příklad najdete v [tématu Výuka: Sdílení prostorových kotev napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořte instanci objektu, `AnchorLocateCriteria` nastavte identifikátory, které hledáte, a vyvolání `CreateWatcher` metody relace poskytnutím aplikace `AnchorLocateCriteria`.