---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ee2c1fd1fc1cad07b14a2c99318be20be30db9c5
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423571"
---
V počáteční sadě školení doporučujeme použít minimálně 30 imagí na značku. Budete také chtít shromáždit několik dalších imagí, pomocí kterých budete model testovat, jakmile bude vyškolený.

Aby bylo možné svůj model efektivně vyškolit, používejte obrázky s vizuální odrůdou. Vyberte obrázky, které se liší podle:
* úhel kamery
* kvůli
* Pozadí
* vizuální styl
* jednotlivé/seskupené (é) věci
* size
* type

Navíc se ujistěte, že všechny vaše školicí image splňují následující kritéria:
* formát. jpg,. png nebo. bmp
* velikost větší než 6MB (4 MB pro obrázky předpovědi)
* méně než 256 pixelů na nejkratší hraně; Všechny bitové kopie, které jsou kratší než tato, se automaticky škálují Custom Vision Service