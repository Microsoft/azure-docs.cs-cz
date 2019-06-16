---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110592"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Vyhledejte prostorových ukotvení cloudu

Nebudou moct vyhledat prostorové ukotvení dříve odeslaný cloudu je jedním z hlavní důvody pro použití knihovně prostorových kotvy Azure. Vyhledat prostorové Cloudová ukotvení, budete potřebovat znát jejich identifikátorů. ID ukotvení mohou být uloženy ve službě back-end vaší aplikace a dostupné všem zařízením, která můžete do ní správné ověření. Příklad najdete v článku [kurzu: Sdílet prostorových kotvy napříč zařízeními](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Vytvoření instance `AnchorLocateCriteria` objektu, nastavit identifikátory hledáte a vyvolání `CreateWatcher` metoda relace tím, že poskytuje vaše `AnchorLocateCriteria`.
