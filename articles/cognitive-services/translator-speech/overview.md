---
title: Co je služba Translator Speech?
titleSuffix: Azure Cognitive Services
description: Rozhraní Translator Speech API se používá k přidání překladu řeči na řeč a řeči na text do aplikací.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: overview
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: 641081e4a01aa5810d569795c10e967e4b46568e
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023573"
---
# <a name="what-is-translator-speech-api"></a>Co je rozhraní Translator Speech API?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Rozhraní Translator Speech API se dá využít k přidání kompletních překladů řeči v reálném čase do aplikací, nástrojů nebo řešení, které vyžadují překlad řeči ve více jazycích, a to bez ohledu na cílový operační systém nebo vývojářský jazyk. Toto rozhraní API lze použít pro překlad řeči na řeč i řeči na text.

Translator Text API je služba Azure, která je součástí [kolekce Azure Cognitive Services API](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive) obsahující algoritmy strojového učení a umělé inteligence v cloudu a je připravená k použití ve vašich vývojových projektech.

Pomocí rozhraní Translator Speech API streamují klientské aplikace do služby mluvený zvuk a dostávají zpět výsledný textový a zvukový stream, který obsahuje rozpoznaný text ve zdrojovém jazyku a jeho překlad do cílového jazyka. Výsledný text se vytváří tak, že se pro příchozí zvukový stream použije automatické rozpoznávání řeči (ASR) na bázi neuronových sítí. Základní výstup ASR se pomocí nové technologie TrueText dále vylepšuje, aby přesněji vyjadřoval záměr uživatele. TrueText například z textu odebírá nepravidelnosti (třeba citoslovce jako hm a zakašlání) a opakovaná slova a nastavuje správnou interpunkci a malá a velká písmena. K dispozici je také možnost maskování nebo vyloučení neslušných slov. Moduly pro rozpoznávání a překlad jsou speciálně natrénované tak, aby zvládly zpracování hovorové řeči. 

Služba Translator Speech používá k určení konce promluvy detekci ticha. Po pauze v hlasové aktivitě začne služba streamovat zpět konečný výsledek pro skončenou promluvu. Služba může odesílat zpět také částečné výsledky, které poskytují rozpoznávání a překlady částí probíhající promluvy. 

U překladu řeči na řeč poskytuje tato služba možnost syntetizace řeči (převodu textu na řeč) z mluveného textu v cílových jazycích. Zvuk se při převodu textu na řeč vytváří ve formátu určeném klientem. K dispozici jsou formáty WAV a MP3.

Rozhraní Translator Speech API používá k zajištění plně duplexního komunikačního kanálu mezi klientem a serverem protokol WebSocket. 

## <a name="about-microsoft-translator"></a>Informace o službě Microsoft Translator
Microsoft Translator je cloudová služba pro strojový překlad. Jádrem této služby jsou služby [Translator Text API](https://www.microsoft.com/en-us/translator/translatorapi.aspx) a Translator Speech API, které se využívají v různých produktech a službách Microsoftu a v aplikacích a pracovních postupech tisíců firem po celém světě, kde umožňují šíření obsahu k cílové skupině po celém světě.

Další informace o [službě Microsoft Translator](https://www.microsoft.com/en-us/translator/home.aspx)

## <a name="microsoft-translator-neural-machine-translation-nmt"></a>Neuronový strojový překlad (NMT) služby Microsoft Translator
Rozhraní Translator Speech API používá k překladům starší statistický strojový překlad (SMT) i novější neuronový strojový překlad (NMT).

U statistického strojového překladu bylo z hlediska zvyšování výkonu dosaženo limitu. Kvalita překladu se u obecných systémů s SMT už nijak významně nezvyšuje. Nová technologie překladu na základě umělé inteligence využívá neuronové sítě.

NMT poskytuje lepší překlady než SMT nejen z hlediska prostého hodnocení kvality překladu, ale také proto, že překlady zní plynuleji a lidštěji. Hlavním důvodem této plynulosti je, že NMT při překladu slov využívá úplný kontext věty. SMT využívá jenom bezprostřední kontext několika slov před a za každým slovem.

Modely NMT jsou v jádru rozhraní API a nejsou viditelné pro koncové uživatele. Jedinými patrnými rozdíly jsou:
* Zlepšení kvality překladu, zejména u jazyků jako čínština, japonština a arabština
* Nekompatibilita se stávajícími funkcemi centra pro úpravy (pro použití s rozhraním Microsoft Translator Text API)

Všechny podporované jazyky pro překlad řeči využívají NMT. Při všech překladech řeči na řeč se proto používá NMT. 

Při překladu řeči na text se může v závislosti na jazykovém páru používat kombinace NMT a SMT. Pokud NMT podporuje cílový jazyk, využívá celý překlad NMT. Pokud NMT nepodporuje cílový jazyk, bude překlad hybridní kombinací NMT a SMT, přičemž jako „pivot“ mezi oběma jazyky se použije angličtina. 

Zobrazit podporované jazyky na [Microsoft.com](https://www.microsoft.com/en-us/translator/languages.aspx) 

Další informace o tom, [jak NMT funguje](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zaregistrovat se](translator-speech-how-to-signup.md)

> [!div class="nextstepaction"]
> [Pustit se do psaní kódu](quickstarts/csharp.md)

## <a name="see-also"></a>Viz také
- [Stránka dokumentace ke službám Cognitive Services](https://docs.microsoft.com/azure/#pivot=products&panel=cognitive)
- [Produktová stránka služeb Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
- [Informace o řešení a cenách](https://www.microsoft.com/en-us/translator/home.aspx) 
