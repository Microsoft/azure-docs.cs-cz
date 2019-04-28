---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232630"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledejte prostorových ukotvení cloudu

Nebudou moct vyhledat prostorové ukotvení dříve odeslaný cloudu je jedním z hlavní důvody pro použití knihovně prostorových kotvy Azure. Vyhledat prostorové Cloudová ukotvení, budete potřebovat znát jejich identifikátorů. ID ukotvení mohou být uloženy ve službě back-end vaší aplikace a dostupné všem zařízením, která můžete do ní správné ověření. Příklad najdete v článku [kurzu: Sdílet prostorových kotvy napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvoření instance `AnchorLocateCriteria` objektu, nastavit identifikátory hledáte a vyvolání `CreateWatcher` metoda relace tím, že poskytuje vaše `AnchorLocateCriteria`.
