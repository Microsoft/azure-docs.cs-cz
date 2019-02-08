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
ms.date: 01/26/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e1bc40f10547eb6c79a41cab8a3ac5fd2f500415
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874070"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API na nejčastější dotazy

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro počítačové zpracování obrazu na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na [Nápověda a podpora na UserVoice](https://cognitive.uservoice.com/)

-----

**Dotaz**: *Trénovat rozhraní API pro počítačové zpracování obrazu používat vlastní značky  Například chci informačního kanálu obrázky plemen cat trénování AI a pak zobrazí hodnotu generace v požadavku AI.*

**Odpověď**: Tato funkce není aktuálně k dispozici. Naši technici jsou však práce zpřístupnit tuto funkci pro počítačové zpracování obrazu.

-----

**Dotaz**: *Počítačové zpracování obrazu slouží místně bez připojení k Internetu?*

**Odpověď**: Momentálně neposkytujeme místní nebo místního řešení.

-----

**Dotaz**: *Jaké jazyky jsou podporovány s pro počítačové zpracování obrazu?*

**Odpověď**: Mezi podporované jazyky patří:

| | | Podporované jazyky | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dánština (da-DK)  | Holandština (nl-NL)     | Angličtina           | Finština (fi-FI)            |Francouzština (fr-FR)
| Němčina (de-DE)  | Řečtina (el-GR)     | Maďarština (hu-HU) | Italština (it-IT)            | Japonština (ja-JP)
| Korejština (ko-KR)  | Norština (nb-NO) | Polština (pl-PL)    | Portugalština (pt-BR) (pt-PT) | Ruština (ru-RU)
| Španělština (es-ES)   | Švédština (sv-SV)     | Turečtina (tr-TR)   |                            |

-----

**Dotaz**: *Pro počítačové zpracování obrazu umožňuje číst talířů licence?*

**Odpověď**: Rozhraní API pro zpracování obrazu nabízí dobrý duplicit text pomocí technologie OCR, ale není aktuálně optimalizované pro licence talířů. Pokoušíme se neustále vylepšovat naše služby a přidali OCR pro automatické rozpoznávání registrační na našem seznamu požadovaných funkcí.

-----

**Otázka:** *Jaké jazyky jsou podporovány pro rozpoznávání textu psaného rukou?*

**Odpověď**: V současné době se podporuje jenom v angličtině.

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
