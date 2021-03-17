---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993049"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledání prostorového kotvícího kotvy

Vyhledávání dříve uloženého cloudového kotvy je jedním z hlavních důvodů použití prostorových kotev Azure. Existuje několik různých způsobů, jak můžete najít cloudovou kotvu. V jednom okamžiku můžete použít jednu strategii sledovacího procesu.
- Vyhledejte kotvy podle identifikátoru.
- Vyhledá kotvy připojené k dříve umístěnému ukotvení. [Tady](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md)najdete informace o souvislostech kotvy.
- Najděte kotvu s využitím [hrubého prostředí](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Pokaždé, když vyhledáte kotvu, se prostorové kotvy Azure pokusí použít shromážděná data prostředí k rozšíření vizuálních informací o kotvě. Pokud máte potíže s umístěním kotvy, může být užitečné vytvořit kotvu a pak ji najít několikrát z různých úhlů a světelných podmínek.

Pokud vyhledáváte cloudové kotvy podle identifikátoru, budete chtít uložit identifikátor prostorové kotvy cloudu do back-endové služby vaší aplikace a zpřístupnit ho pro všechna zařízení, která se k němu můžou správně ověřit. Příklad najdete v tématu [kurz: sdílení prostorových ukotvení napříč zařízeními](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Vytvořte instanci `AnchorLocateCriteria` objektu, nastavte identifikátory, které hledáte, a volejte `CreateWatcher` metodu v relaci tím, že poskytnete `AnchorLocateCriteria` .