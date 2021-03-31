---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 9f8eadea198bbae3de2ffc1b3aaac48925719586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100106285"
---
V počáteční sadě školení doporučujeme použít minimálně 30 imagí na značku. Budete také chtít shromáždit několik dalších imagí, pomocí kterých budete model testovat, jakmile bude vyškolený.

Aby bylo možné svůj model efektivně vyškolit, používejte obrázky s vizuální odrůdou. Vyberte obrázky, které se liší podle:
* úhel kamery
* kvůli
* pozadí
* vizuální styl
* jednotlivé/seskupené (é) věci
* size
* typ

Navíc se ujistěte, že všechny vaše školicí image splňují následující kritéria:
* formát. jpg,. png,. bmp nebo. gif
* velikost větší než 6MB (4 MB pro obrázky předpovědi)
* méně než 256 pixelů na nejkratší hraně; Všechny bitové kopie, které jsou kratší než tato, se automaticky škálují Custom Vision Service

> [!NOTE]
> Potřebujete k dokončení školení širší sadu imagí? Studnicí, projekt garáže společnosti Microsoft, umožňuje shromažďovat a kupovat sady imagí pro účely školení. Po shromáždění imagí si je můžete stáhnout a pak je importovat do projektu Custom Vision obvyklým způsobem. Další informace najdete na [stránce studnicí](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .