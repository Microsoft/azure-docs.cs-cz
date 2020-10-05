---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "70130072"
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
