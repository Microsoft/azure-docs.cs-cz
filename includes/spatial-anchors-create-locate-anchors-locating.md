---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887651"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledání prostorového kotvícího kotvy

Vyhledávání dříve uloženého cloudového kotvy je jedním z hlavních důvodů použití prostorových kotev Azure. Existuje několik různých způsobů, jak můžete najít cloudovou kotvu. V jednom okamžiku můžete použít jednu strategii sledovacího procesu.
- Vyhledejte kotvy podle identifikátoru.
- Vyhledá kotvy připojené k dříve umístěnému ukotvení. [Tady](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)najdete informace o souvislostech kotvy.
- Najděte kotvu s využitím [hrubého prostředí](/azure/spatial-anchors/concepts/coarse-reloc/).

Pokud vyhledáváte cloudové kotvy podle identifikátoru, budete chtít uložit identifikátor prostorové kotvy cloudu do back-endové služby vaší aplikace a zpřístupnit ho pro všechna zařízení, která se k němu můžou správně ověřit. Příklad najdete v tématu [kurz: sdílení prostorových ukotvení napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořte instanci objektu `AnchorLocateCriteria`, nastavte identifikátory, které hledáte, a volejte metodu `CreateWatcher` v relaci tím, že poskytnete `AnchorLocateCriteria`.