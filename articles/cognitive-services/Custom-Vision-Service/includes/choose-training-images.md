---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 5508199212b788e13a27b3c97b8fb7174dc92f32
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99531041"
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
> Potřebujete k dokončení školení širší sadu imagí? Studnicí, projekt garáže společnosti Microsoft, umožňuje shromažďovat a kupovat sady imagí pro účely školení. Po shromáždění imagí si je můžete stáhnout a pak je importovat do projektu Custom Vision obvyklým způsobem. Další informace najdete na [stránce studnicí](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) .