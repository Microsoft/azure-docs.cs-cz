---
title: Souhrn rychlých startů ke službě Custom Vision
titleSuffix: Azure Cognitive Services
description: V těchto rychlých startech analyzujete obrázek, vytvoříte miniaturu obrázku a extrahujete tištěný a ručně psaný text pomocí rozhraní API služby Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8fd27fc0038e91531fbf05942336ce7d6fc34e37
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310011"
---
# <a name="quickstart-summary"></a>Rychlý start: Souhrn

Tyto rychlé starty obsahují informace a ukázky kódu, které vám pomůžou rychle začít používat službu počítačového zpracování obrazu.

Ukázky provádí přímé volání HTTP do rozhraní API pro počítačové zpracování obrazu. V části *rychlých startů sad SDK* najdete ukázky použití klientských knihoven počítačového zpracování obrazu, se kterými můžete snadno využívat volání HTTP.

Pokud chcete rychle vyzkoušet rozhraní API pro počítačové zpracování obrazu, vyzkoušejte [testovací konzolu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

Službu počítačového zpracování obrazu můžete použít k provedení následujících úkolů:

* Analýza vzdáleného obrázku
* Analýza místního obrázku
* Rozpoznání celebrit a památek (doménové modely)
* Inteligentní vygenerování miniatury
* Rozpoznání a extrakce tištěného textu (OCR) z obrázku
* Rozpoznání a extrakce ručně psaného textu z obrázku

Kód jednotlivých ukázek je podobný. Zdůrazňují ale různé funkce počítačového zpracování obrazu společně s různými technikami pro výměnu dat se službou, například:

* _Vygenerování miniatury_ vrátí obrázek jako pole bajtů v textu odpovědi.
* _Analýza místního obrázku_ vyžaduje, aby obrázek byl součástí žádosti jako pole bajtů.
* _Extrakce ručně psaného textu_ vyžaduje k získání textu dvě volání.

## <a name="summary"></a>Souhrn

| Rychlý start               | Parametry žádosti                          | Odpověď          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analýza vzdáleného obrázku   | visualFeatures=Categories,Description,Color | Řetězec ve formátu JSON       |
| Analýza místního obrázku    | data=image_data (pole bajtů)                | Řetězec ve formátu JSON       |
| Rozpoznání celebrit       | model=celebrities                           | Řetězec ve formátu JSON       |
| Vytvoření miniatury     | width=200&height=150&smartCropping=true     | pole bajtů        |
| Extrakce tištěného textu     | language=unk&detectOrientation=true         | Řetězec ve formátu JSON       |
| Extrakce rukou psaného textu | handwriting=true                            | URL, řetězec JSON  |

## <a name="next-steps"></a>Další postup

Prozkoumejte rozhraní API pro počítačové zpracování obrazu používané pro analýzu obrázku, zjišťování celebrit a památek, vytvoření miniatury a extrahování tištěného a ručně psaného textu.

> [!div class="nextstepaction"]
> [Prozkoumat rozhraní API pro počítačové zpracování obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
