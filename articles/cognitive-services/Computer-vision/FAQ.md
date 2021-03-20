---
title: Nejčastější dotazy – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e8ab205a43e5cb1e8e2b96dbd9600e1fceb29403
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "68564603"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>rozhraní API pro počítačové zpracování obrazu nejčastějších dotazech

> [!TIP]
> Pokud nemůžete najít odpovědi na vaše otázky v těchto nejčastějších dotazech, zkuste požádat o rozhraní API pro počítačové zpracování obrazu komunitu na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo kontaktujte [pomoc a podporu na webu UserVoice](https://cognitive.uservoice.com/) .

---

**Otázka**: můžu *rozhraní API pro počítačové zpracování obrazu použít vlastní značky?  Například chci, aby se v obrázcích v nabídce Cat použily klíčové slovo "vlak", a pak získá hodnotu plemeny na žádost AI.*

**Odpověď**: Tato funkce není momentálně k dispozici. Naši technici ale pracují na tom, aby tuto funkci Počítačové zpracování obrazu.

---

**Otázka**: *je možné počítačové zpracování obrazu použít místně bez připojení k Internetu?*

**Odpověď**: v současnosti nenabízíme místní nebo místní řešení.

---

**Otázka**: *dá se počítačové zpracování obrazu použít ke čtení desek licencí?*

**Odpověď**: rozhraní Vision API nabízí dobrý detekci textu s rozpoznáváním OCR, ale není aktuálně optimalizované pro licenční štítky. Neustále se snažíme vylepšit naše služby a přidat rozpoznávání OCR pro rozpoznávání automatických licencí do našeho seznamu žádostí o funkce.

---

**Otázka**: *Jaké typy povrchů psaní se podporují pro rozpoznávání rukopisu?*

**Odpověď**: technologie funguje s různými druhy povrchů, včetně tabulí, dokumentu White Paper a žlutých rychlých poznámek.

---

**Otázka**: *Jak dlouho trvá operace rozpoznávání rukopisu?*

**Odpověď**: množství času, které trvá, závisí na délce textu. U delších textů může trvat až několik sekund. Proto po dokončení operace rozpoznávání ručního textu budete možná muset počkat, než budete moci načíst výsledky pomocí operace získat rukopisný text operace.

---

**Otázka**: *jak technologie rozpoznávání rukopisu zpracovává text, který byl vložen pomocí blikajícího kurzoru uprostřed čáry?*

**Odpověď**: Tento text se vrátí jako samostatný řádek operace rozpoznávání rukopisu.

---

**Otázka**: *jak technologie rozpoznávání rukopisu zpracovává křížová slova nebo řádky?*

**Odpověď**: Pokud jsou slova rozložená na více řádků, aby je bylo možné vykreslit nerozpoznatelná, operace rozpoznávání rukopisu je nevybrala. Pokud se ale slova přecházejí pomocí jednoho řádku, považuje se tato křížení za šum a tato slova se pořád vybírají operací rozpoznávání rukopisu.

---

**Otázka**: *Jaké orientace textu je pro technologii rozpoznávání rukopisu podporovaná?*

**Odpověď**: v rámci operace rozpoznávání rukopisu může získat text orientovaný na úhly až do přibližně 30 stupňů až 40 stupňů.

---
