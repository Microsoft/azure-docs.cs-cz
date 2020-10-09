---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006488"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledání prostorového kotvícího kotvy

Vyhledávání dříve uloženého cloudového kotvy je jedním z hlavních důvodů použití prostorových kotev Azure. Existuje několik různých způsobů, jak můžete najít cloudovou kotvu. V jednom okamžiku můžete použít jednu strategii sledovacího procesu.
- Vyhledejte kotvy podle identifikátoru.
- Vyhledá kotvy připojené k dříve umístěnému ukotvení. [Tady](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)najdete informace o souvislostech kotvy.
- Najděte kotvu s využitím [hrubého prostředí](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Pokaždé, když vyhledáte kotvu, se prostorové kotvy Azure pokusí použít shromážděná data prostředí k rozšíření vizuálních informací o kotvě. Pokud máte potíže s umístěním kotvy, může být užitečné vytvořit kotvu a pak ji najít několikrát z různých úhlů a světelných podmínek.

Pokud vyhledáváte cloudové kotvy podle identifikátoru, budete chtít uložit identifikátor prostorové kotvy cloudu do back-endové služby vaší aplikace a zpřístupnit ho pro všechna zařízení, která se k němu můžou správně ověřit. Příklad najdete v tématu [kurz: sdílení prostorových ukotvení napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořte instanci `AnchorLocateCriteria` objektu, nastavte identifikátory, které hledáte, a volejte `CreateWatcher` metodu v relaci tím, že poskytnete `AnchorLocateCriteria` .