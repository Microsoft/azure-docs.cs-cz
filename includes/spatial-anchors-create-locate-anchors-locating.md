---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907667"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledejte prostorových ukotvení cloudu

Vyhledat prostorové Cloudová ukotvení, budete potřebovat znát jejich identifikátorů. ID ukotvení, může být uložené ve službě back-endu vaší aplikace a je dostupná pro všechna zařízení, které lze k němu správné ověření. Příklad najdete v článku [kurzu: Sdílet prostorových kotvy napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvořit instanci objektu AnchorLocateCriteria, nastavte identifikátory, které hledáte a tím, že poskytuje vaše AnchorLocateCriteria vyvolat metodu CreateWatcher v relaci.
