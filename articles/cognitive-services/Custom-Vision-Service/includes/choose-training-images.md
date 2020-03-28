---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: ce67062f83ad10703dfb1f0ee1f3741e698fb450
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70130072"
---
Minimálně doporučujeme použít alespoň 30 obrázků na značku v počáteční trénovací sadě. Budete také chtít shromáždit několik dalších obrázků k testování modelu, jakmile je vyškolen.

Chcete-li efektivně trénovat model, použijte obrázky s vizuální rozmanitostí. Vyberte obrázky, které se liší podle:
* úhel kamery
* Osvětlení
* pozadí
* vizuální styl
* individuální/seskupený subjekt (předměty)
* velikost
* type

Dále se ujistěte, že všechny tréninkové obrázky splňují následující kritéria:
* Formát JPG, .png, .bmp nebo .gIF
* ne větší než 6 MB (4 MB pro predikční obrázky)
* nejméně 256 pixelů na nejkratším okraji; všechny obrázky kratší, než je tato, budou automaticky navýšeny službou Custom Vision Service
