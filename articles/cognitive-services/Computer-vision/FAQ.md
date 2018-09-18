---
title: Nejčastější dotazy k Computer Vision API
titlesuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API pro počítačové zpracování obrazu ve službě Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c0a4e981a290b9a758c8401a75e546c61618b45
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983897"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Computer Vision API na nejčastější dotazy

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nemůžete najít odpovědi na své dotazy v tomto dokumentu, zkuste se zeptat v komunitě rozhraní API pro počítačové zpracování obrazu na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na [Nápověda a podpora na UserVoice](https://cognitive.uservoice.com/)

-----

**Dotaz**: *můžete trénování rozhraní API pro počítačové zpracování obrazu používat vlastní značky?  Například chci informačního kanálu obrázky plemen cat trénování AI a pak zobrazí hodnotu generace v požadavku AI.*

**Odpověď**: Tato funkce momentálně není k dispozici. Naši technici jsou však práce zpřístupnit tuto funkci pro počítačové zpracování obrazu.

-----

**Dotaz**: *můžete pro počítačové zpracování obrazu se dá používat místně bez připojení k Internetu?*

**Odpověď**: aktuálně nenabízíme místní nebo místního řešení.

-----

**Dotaz**: *jazyky, které podporují pro počítačové zpracování obrazu?*

**Odpověď**: podporované jazyky patří:

| | | Podporované jazyky | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dánština (da-DK)  | Holandština (nl-NL)     | Angličtina           | Finština (fi-FI)            |Francouzština (fr-FR)
| Němčina (de-DE)  | Řečtina (el-GR)     | Maďarština (hu-HU) | Italština (it-IT)            | Japonština (ja-JP)
| Korejština (ko-KR)  | Norština (nb-NO) | Polština (pl-PL)    | Portugalština (pt-BR) (pt-PT) | Ruština (ru-RU)
| Španělština (es-ES)   | Švédština (sv-SV)     | Turečtina (tr jednotek Propustnosti)   |                            |

-----

**Dotaz**: *můžete pro počítačové zpracování obrazu použít ke čtení talířů licence?*

**Odpověď**: rozhraní API pro zpracování obrazu nabízí dobrý duplicit text pomocí technologie OCR, ale není aktuálně optimalizované pro licence talířů. Pokoušíme se neustále vylepšovat naše služby a přidali OCR pro automatické rozpoznávání registrační na našem seznamu požadovaných funkcí.

-----

**Otázka:** *jazyky, které podporují rozpoznávání textu psaného rukou?*

**Odpověď**: v současné době se podporuje jenom v angličtině.

-----

**Dotaz**: *jaké typy psaní plochy jsou podporovány pro rozpoznávání textu psaného rukou?*

**Odpověď**: Tato technologie funguje s různými druhy zařízení Surface, včetně bločky nebo Tabule, dokument white paper a žluté.

-----

**Dotaz**: *jak dlouho operace rozpoznávání textu psaného rukou trvá?*

**Odpověď**: množství času potřebného závisí na délce textu. Pro delší text může trvat až na několik sekund. Proto po dokončení operace rozpoznat rukou Text, budete muset počkat, než můžete načíst pomocí operace získat rukou výsledek operace Text.

-----

**Dotaz**: *jak text popisovač technologie rozpoznávání rukopisu, který byl vložen pomocí stříška uprostřed řádku?*

**Odpověď**: takového textu se vrátí jako samostatný řádek operací rozpoznávání rukopisu.

-----

**Dotaz**: *jak technologii rozpoznávání rukopisu zpracovává přeškrtnutý slova nebo řádky?*

**Odpověď**: překročení slova s více řádky k vykreslení je nelze rozpoznat, operace rozpoznávání textu psaného rukou nebude sesbírejte je. Ale překročení slova pomocí jeden řádek, tento přechod je považován za šumu a slova stále získat vyzvednou operace rozpoznávání rukopisu.

-----

**Dotaz**: *jaké orientace textu podporují technologii rozpoznávání rukopisu?*

**Odpověď**: Text orientovaný na úhly až přibližně 30 stupňů na 40 stupně může získat vyzvednou operace rozpoznávání rukopisu.

-----
