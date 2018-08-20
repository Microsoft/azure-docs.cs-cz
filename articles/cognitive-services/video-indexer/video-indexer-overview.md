---
title: Přehled služby Azure Video Indexer | Microsoft Docs
description: Toto téma nabízí přehled služby Azure Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: overview
ms.date: 07/25/2018
ms.author: nolachar
ms.openlocfilehash: f52c4af29d0c7de8b5edbe869640ffc5dddb5c5e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397887"
---
# <a name="what-is-video-indexer-preview"></a>Co je Video Indexer? (Preview)

Video Indexer je cloudová aplikace sestavená s využitím služeb Azure Media Analytics, Cognitive Services (jako jsou rozhraní API pro rozpoznávání tváře, Microsoft Translator, rozhraní API pro počítačové zpracování obrazu a služba Custom Speech) a Azure Search. Pomocí technologií umělé inteligence umožňuje extrahovat z videí následující přehledy:

- **Automatická detekce jazyka:** Video Indexer dokáže automaticky rozpoznat jazyk videa. Automatické rozpoznávání jazyka v současné době podporuje angličtinu, španělštinu, francouzštinu, němčinu, italštinu, čínštinu (zjednodušenou), japonštinu a ruštinu. Pokud jazyk nejde rozpoznat, přejde se zpátky k angličtině.
- **Přepis zvuku:** Video Indexer má funkce pro převod řeči na text, které zákazníkům umožňují získat přepis mluveného slova. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, čínština (zjednodušená), portugalština (Brazílie), japonština a ruština (a v budoucnu se chystá řada dalších jazyků). 
- **Identifikace a sledování tváří:** Technologie rozpoznávání tváře umožňují detekovat tváře ve videu. Detekované tváře se porovnávají s databází celebrit a zjišťuje se, které celebrity se ve videu vyskytují. Zákazníci také mohou označit tváře, které se neodpovídají celebritám. Video Indexer na základě těchto označení sestaví model tváří a dokáže tyto tváře rozpoznávat ve videích nahraných v budoucnu.
- **Indexování mluvčích:** Video Indexer má schopnost mapovat a pochopit, který mluvčí pronesl která slova a kdy.
- **Vizuální rozpoznávání textu:** Pomocí této technologie služba Video Indexer extrahuje text, který se zobrazuje ve videích.  
- **Detekce hlasových aktivit:** Tato detekce umožňuje Video Indexeru oddělit šum na pozadí od hlasových aktivit. 
- **Rozpoznávání scén:** Video Indexer má schopnost provádět vizuální analýzu k toho, kdy se ve videu změní scéna.
- **Extrakce klíčových snímků:** Video Indexer ve videu automaticky rozpozná klíčové snímky. 
- **Analýza mínění:** Video Indexer provádí analýzu mínění pro text extrahovaný pomocí převodu řeči na text a optického rozpoznávání znaků a poskytuje informace ve formě kladného, záporného nebo neutrálního mínění spolu s časovými kódy.
- **Překlad:** Video Indexer dokáže přeložit přepis zvukového záznamu z jednoho jazyka do druhého. Mezi podporované jazyky patří angličtina, španělština, francouzština, němčina, italština, zjednodušená čínština, brazilská portugalština, japonština a ruština. Po překladu uživatel může dokonce získat titulky ve videopřehrávači v jiných jazycích.
- **Moderování vizuálního obsahu:** Tato technologie umožňuje ve videích detekovat obsah pro dospělé nebo nevhodný obsah a dá se využít k filtrování obsahu. 
- **Extrakce klíčových slov:** Video Indexer extrahuje klíčová slova na základě přepisu mluveného slova a textu rozpoznaného pomocí modulu pro rozpoznání vizuálního textu.
- **Označení:** Video Indexer poskytuje označení pro vizuální objekty, jako je kočka, pes, tabulka nebo auto, a také pro akce, jako stojící, běžící nebo letící.
- **Značky:** Video Indexer extrahuje obchodní značky na základě přepisu mluveného slova a textu rozpoznaného pomocí modulu pro rozpoznání vizuálního textu.

Jakmile Video Indexer dokončí zpracování a analýzu, můžete přehledné informace o videích revidovat, kurátorovat, prohledávat a publikovat.

Služba Video Indexer dokáže splnit vaše požadavky, ať už jste vývojář, nebo správce obsahu. Správci obsahu mohou využívat webový portál služby Video Indexer k využití této služby, aniž by museli napsat jediný řádek kódu. Další informace najdete v tématu věnovaném [zahájení práce s portálem služby Video Indexer](video-indexer-get-started.md). Vývojáři mohou využít rozhraní API ke zpracování obsahu ve velkém měřítku, viz téma věnované [použití rozhraní REST API služby Video Indexer](video-indexer-use-apis.md). Tato služba také zákazníkům umožňuje používat widgety k publikování datových proudů videa a extrahovaných přehledů ve vlastních aplikacích. Další informace najdete v tématu [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md).

K registraci této služby můžete využít existující účet služeb AAD, LinkedIn, Facebook, Google nebo MSA. Další informace najdete v tématu [Začínáme](video-indexer-get-started.md).

## <a name="scenarios"></a>Scénáře

Dál najdete několik scénářů, ve kterých se Video Indexer může hodit

- Hledání – Přehledy extrahované z videa se dají využít k vylepšení vyhledávání v knihovně videí. Například indexování mluveného slova a tváří umožňuje ve videu vyhledávat okamžiky, kdy konkrétní osoba říká určitá slova nebo kdy jsou dva lidé vidět pohromadě. Hledání založené na takovýchto přehledech z videí je užitečné pro zpravodajské agentury, vzdělávací instituce, provozovatele vysílání, vlastníky zábavního obsahu, podnikové obchodní aplikace a obecně pro libovolné obory využívající knihovnu videí, ve kterých musí uživatelé vyhledávat.

- Finanční zhodnocení – Video Indexer umožňuje zvýšit hodnotu videí. Například obory, které závisejí na výnosech z reklam (například sdělovací prostředky, sociální média atd.), mohou využít extrahované přehledy jako další signály pro reklamní server a poskytovat díky nim relevantnější reklamy (reklama na sportovní obuv je relevantnější během fotbalového utkání než při plaveckých soutěžích).

- Zapojení uživatelů – Video Insights se dá využít k lepšímu zapojení uživatelů díky možnosti předkládání relevantních videookamžiků. Jako příklad si představte vzdělávací video, které prvních 30 minut vysvětluje kulové plochy a dalších 30 minut pyramidy. Studentovi, který si čte o pyramidách, by víc pomohlo, kdyby se video nastavilo tak, aby se spustilo od značky 30 minut.

Další informace najdete na tomto [blogu](http://aka.ms/videoindexerblog).

## <a name="next-steps"></a>Další kroky

Jste připraveni začít pracovat s Video Indexerem. Další informace najdete v následujících článcích:

- [Začínáme s portálem služby Video Indexer](video-indexer-get-started.md)
- [Zpracování obsahu pomocí rozhraní REST API služby Video Indexer](video-indexer-use-apis.md)
- [Vložení vizuálních widgetů do vaší aplikace](video-indexer-embed-widgets.md)
