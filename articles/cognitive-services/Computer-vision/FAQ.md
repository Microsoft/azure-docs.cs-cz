---
title: Časté otázky – pro počítačové zpracování obrazu
titlesuffix: Azure Cognitive Services
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
ms.openlocfilehash: 579a47e70f292222914723606d032737b98822bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498776"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API na nejčastější dotazy

> [!TIP]
> Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro počítačové zpracování obrazu na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na [Nápověda a podpora na UserVoice](https://cognitive.uservoice.com/)

-----

**Dotaz**: *Trénovat rozhraní API pro počítačové zpracování obrazu používat vlastní značky  Například chci informačního kanálu obrázky plemen cat trénování AI a pak zobrazí hodnotu generace v požadavku AI.*

**Odpověď**: Tato funkce není aktuálně k dispozici. Naši technici jsou však práce zpřístupnit tuto funkci pro počítačové zpracování obrazu.

-----

**Dotaz**: *Počítačové zpracování obrazu slouží místně bez připojení k Internetu?*

**Odpověď**: Momentálně neposkytujeme místní nebo místního řešení.

-----

**Dotaz**: *Pro počítačové zpracování obrazu umožňuje číst talířů licence?*

**Odpověď**: Rozhraní API pro zpracování obrazu nabízí dobrý duplicit text pomocí technologie OCR, ale není aktuálně optimalizované pro licence talířů. Pokoušíme se neustále vylepšovat naše služby a přidali OCR pro automatické rozpoznávání registrační na našem seznamu požadovaných funkcí.

-----

**Dotaz**: *Jaké typy psaní plochy jsou podporovány pro rozpoznávání textu psaného rukou?*

**Odpověď**: Tato technologie funguje s různými druhy zařízení Surface, včetně bločky nebo Tabule, dokument white paper a žluté.

-----

**Dotaz**: *Jak dlouho operace rozpoznávání textu psaného rukou trvá?*

**Odpověď**: Množství času potřebného závisí na délce textu. Pro delší text může trvat až na několik sekund. Proto po dokončení operace rozpoznat rukou Text, budete muset počkat, než můžete načíst pomocí operace získat rukou výsledek operace Text.

-----

**Dotaz**: *Jak technologii rozpoznávání ručně psaného textu zpracovává text, který byl vložen pomocí stříška uprostřed řádku?*

**Odpověď**: Takové textu se vrátí jako samostatný řádek operací rozpoznávání rukopisu.

-----

**Dotaz**: *Jak technologii rozpoznávání rukopisu zpracovává přeškrtnutý slova nebo řádky?*

**Odpověď**: Překročení slova s více řádky k vykreslení je nelze rozpoznat, operace rozpoznávání textu psaného rukou nebude sesbírejte je. Ale překročení slova pomocí jeden řádek, tento přechod je považován za šumu a slova stále získat vyzvednou operace rozpoznávání rukopisu.

-----

**Dotaz**: *Jaké orientace textu podporují technologii rozpoznávání rukopisu?*

**Odpověď**: Text orientovaný na úhly až přibližně 30 stupňů na 40 stupně může získat neexistoval operace rozpoznávání rukopisu.

-----
