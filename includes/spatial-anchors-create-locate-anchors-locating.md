---
ms.openlocfilehash: ac9fe1d367dba0ebdf4250b3213f191ced758dd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694533"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledání prostorového kotvícího kotvy

Vyhledávání dříve uloženého cloudového kotvy je jedním z hlavních důvodů použití prostorových kotev Azure. Existuje několik různých způsobů, jak můžete najít cloudovou kotvu. V jednom okamžiku můžete použít jednu strategii sledovacího procesu.
- Vyhledejte kotvy podle identifikátoru.
- Vyhledá kotvy připojené k dříve umístěnému ukotvení. [Tady](/azure/spatial-anchors/concepts/anchor-relationships-way-finding.md)najdete informace o souvislostech kotvy.
- Najděte kotvu s využitím [hrubého prostředí](/azure/spatial-anchors/concepts/coarse-reloc.md).

Pokud vyhledáváte cloudové kotvy podle identifikátoru, budete chtít uložit identifikátor prostorové kotvy cloudu do back-endové služby vaší aplikace a zpřístupnit ho pro všechna zařízení, která se k němu můžou správně ověřit. Příklad najdete v tématu [kurz: sdílení prostorových ukotvení napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořte instanci objektu `AnchorLocateCriteria`, nastavte identifikátory, které hledáte, a volejte metodu `CreateWatcher` v relaci tím, že poskytnete `AnchorLocateCriteria`.