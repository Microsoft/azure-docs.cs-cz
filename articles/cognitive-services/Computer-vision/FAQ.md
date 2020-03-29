---
title: Nejčastější dotazy - Počítačové vidění
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro počítačové zpracování obrazu ve službách Azure Cognitive Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68564603"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Nejčastější dotazy týkající se rozhraní API pro počítačové zpracování obrazu

> [!TIP]
> Pokud v těchto nejčastějších dotazech nemůžete najít odpovědi na své otázky, zkuste se zeptat komunity rozhraní API pro počítačové zpracování obrazu v [aplikaci StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte [na nápovědu a podporu na webu UserVoice.](https://cognitive.uservoice.com/)

---

**Otázka**: *Mohu trénovat rozhraní API pro počítačové zpracování obrazu tak, aby používalo vlastní značky?  Například bych se chtěl nakrmit na obrázcích kočičích plemen, abych "vyškolil" AI, a pak jsem získal hodnotu plemene na žádost ai.*

**Odpověď**: Tato funkce není momentálně k dispozici. Naši inženýři však pracují na tom, aby tuto funkci přinesli do počítačového vidění.

---

**Otázka**: *Lze počítačové vidění používat lokálně bez připojení k internetu?*

**Odpověď**: V současné době nenabízíme místní ani místní řešení.

---

**Otázka**: *Lze počítačové vidění použít ke čtení poznávacích značek?*

**Odpověď**: Rozhraní Vision API nabízí dobrou detekci textu s rozpoznáváním OCR, ale v současné době není optimalizováno pro registrační značky. Neustále se snažíme zlepšovat naše služby a přidali OCR pro automatické rozpoznávání poznávacích značek do našeho seznamu požadavků na funkce.

---

**Otázka**: *Jaké typy ploch psaní jsou podporovány pro rozpoznávání rukopisu?*

**Odpověď**: Technologie pracuje s různými druhy povrchů, včetně tabulí, bílého papíru a žlutých lepivých poznámek.

---

**Otázka**: *Jak dlouho trvá operace rozpoznávání rukopisu?*

**Odpověď**: Doba, kterou trvá, závisí na délce textu. U delších textů může trvat až několik sekund. Proto po dokončení operace Rozpoznat ručně psaný text může být nutné počkat, než budete moci načíst výsledky pomocí operace Získat ručně psaný text operace výsledek.

---

**Otázka**: *Jak technologie rozpoznávání rukopisu zpracovává text, který byl vložen pomocí stříšky uprostřed řádku?*

**Odpověď**: Tento text je vrácen jako samostatný řádek operací rozpoznávání rukopisu.

---

**Otázka**: *Jak technologie rozpoznávání rukopisu zpracovává přeškrtnutá slova nebo řádky?*

**Odpověď**: Pokud jsou slova přeškrtnuta s více řádky, aby byla k dispozici, operace rozpoznávání rukopisu je nezvedne. Pokud jsou však slova přeškrtnuta pomocí jednoho řádku, je tento přechod považován za šum a slova jsou stále zachycena operací rozpoznávání rukopisu.

---

**Otázka**: *Jaké textové orientace jsou podporovány pro technologii rozpoznávání rukopisu?*

**Odpověď**: Text orientovaný pod úhlem až kolem 30 stupňů až 40 stupňů může být zachycen operací rozpoznávání rukopisu.

---
