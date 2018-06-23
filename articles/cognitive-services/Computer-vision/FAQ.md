---
title: Časté otázky k počítači vizi rozhraní API | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se rozhraní API vize počítače v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/26/2017
ms.author: kefre
ms.openlocfilehash: 5c862dd2fb26a005f4e785673a4e9358ecf9286f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342418"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Nejčastější dotazy k počítači vize rozhraní API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Pokud nenajdete odpovědi na otázky v tohoto článku, zkuste žádostí počítače vize API komunity na [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) nebo se obraťte na [Nápověda a podpora na UserVoice](https://cognitive.uservoice.com/)

-----

**Otázka**: *můžete cvičení API vize počítače mají používat vlastní značky?  Například nastavit jako ke kanálu v obrázcích plemen cat cvičení AI a pak zobrazí hodnota plemeno na požadavek AI.*

**Odpověď**: Tato funkce není momentálně k dispozici. Naše technici však pracují zobrazíte tuto funkci můžete vize počítače.

-----

**Otázka**: *vize počítače můžete použít místně bez připojení k Internetu?*

**Odpověď**: aktuálně nenabízíme místní nebo místní řešení.

-----

**Otázka**: *podporované jazyky s vize počítače?*

**Odpověď**: mezi podporované jazyky patří:

| | | Podporované jazyky | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dánština (da-DK)  | Holandština (nl-NL)     | Angličtina           | Finština (fi-FI)            |Francouzština (fr-FR)
| Němčina (de-DE)  | Řečtina (el-GR)     | Maďarština (hu-HU) | Italština (it-IT)            | Japonština (ja-JP)
| Korejština (ko-KR)  | Norština (nb-NO) | Polština (pl-PL)    | Portugalština (pt-BR) (pt-PT) | Ruština (ru-RU)
| Španělština (es-ES)   | Švédština (sv-SV)     | Turečtina (tr-TU)   |                            |

-----

**Otázka**: *vize počítače můžete použít ke čtení desky licence?*

**Odpověď**: rozhraní API vize nabízí dobrý text vyhledávání s rozpoznávání znaků, ale není aktuálně optimalizované pro desky licence. Neustále Snažíme se zlepšování našich služeb a přidali rozpoznávání znaků pro automatické rozpoznávání registrační na našem seznamu požadovaných funkcí.

-----

**Otázka:** *podporované jazyky pro rozpoznávání rukopisu?*

**Odpověď**: v současné době podporuje pouze angličtina.

-----

**Otázka**: *jaké typy zápis povrchy jsou podporovány pro rozpoznávání rukopisu?*

**Odpověď**: technologie funguje s různými druhy povrchy, včetně Tabule, dokument white paper a žlutý rychlé poznámky.

-----

**Otázka**: *jak dlouho operaci rozpoznávání rukopisu trvá?*

**Odpověď**: množství času potřebného závisí na délku textu. Pro delší texty může trvat až na několik sekund. Proto po dokončení operace rozpoznat rukou Text, můžete počkat, než můžete načíst výsledky pomocí operace získání rukou výsledek operace Text.

-----

**Otázka**: *jak funguje text popisovač technologie rozpoznávání rukopisu, který byl vložen pomocí šipka nahoru uprostřed řádku?*

**Odpověď**: takový text se vrátí jako samostatný řádek operace rozpoznávání rukopisu.

-----

**Otázka**: *jak technologii rozpoznávání rukopisu přeškrtnutý slova nebo popisovače řádky?*

**Odpověď**: Pokud slova se překročí s více řádky k vykreslení je nelze rozpoznat, operace rozpoznávání rukopisu se vyzvedávat je. Ale pokud slova se překročí pomocí jeden řádek, že překročení se považuje za šumu a slova stále získat zachyceny operace rozpoznávání rukopisu.

-----

**Otázka**: *jakou směrů text jsou podporovány pro technologii rozpoznávání rukopisu?*

**Odpověď**: Text zaměřené na konkrétní na úhly až přibližně 30 stupňů do 40 stupňů může získat zachyceny operace rozpoznávání rukopisu.

-----
