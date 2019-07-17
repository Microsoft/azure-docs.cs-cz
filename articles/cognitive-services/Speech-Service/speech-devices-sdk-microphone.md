---
title: Pole doporučení řeči Devices SDK mikrofon – hlasové služby
titleSuffix: Azure Cognitive Services
description: Rozpoznávání řeči Devices SDK mikrofon pole doporučení. Následující pole geometrie se doporučují pro použití se službou Microsoft Stack zvuk. Umístění zvuku zdrojů a odmítnutí hluk lepší s více mikrofony se závislostmi na konkrétní aplikace, uživatelské scénáře a provedení zařízení.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: cb30b476471e140f96fa1d159e9a16898f529607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277496"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Doporučení pole zařízení SDK mikrofon řeči

V tomto článku se dozvíte, jak navrhnout mikrofon pole pro sadou SDK pro řeč zařízení.

Sadou SDK pro řeč zařízení funguje nejlépe s polem mikrofon, která byla navržena podle následujících pokynů, včetně výběru geometrie a součást mikrofon. Pokyny k je také zadána na integraci a elektrické aspekty.

## <a name="microphone-geometry"></a>Mikrofon geometrie

Následující pole geometrie se doporučují pro použití se službou Microsoft Stack zvuk. Umístění zvuku zdrojů a odmítnutí hluk lepší s více mikrofony se závislostmi na konkrétní aplikace, uživatelské scénáře a provedení zařízení.

|          | Cyklické pole    |       |  Lineární pole              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |<img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/>|<img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/>|<img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/>|
| \# Mikrofonu  | 7                 | 4                 | 4              | 2              |
| Geometrie | 6 vnější, 1 centrum, protokolu Radius 42,5 mm, rovnoměrně rozmístěné.| 3 vnější, 1 centrum, protokolu Radius 42,5 mm, rovnoměrně rozmístěné. | Délka = 120 mm mezery = 40 mm | Mezery = 40 mm |

Mikrofon kanály by měl být seřazeny podle toho, číslování použité v ukázkách pro každou nad pole, zvýšení od 0.  Zvukový zásobníku Microsoft bude vyžadovat další referenční posloupnost přehrávání zvuku provádět zrušení zpětné vazby.

## <a name="component-selection"></a>Výběr součásti

Přesně reprodukovat signál zdarma šumu a narušení by měl být vybrány součásti mikrofon.

Při výběru mikrofonů doporučené vlastnosti patří:

| Parametr                         | Doporučené                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \>= 65 dB (dBSPL signál 94 1 kHz, akustického)   |
| Odpovídající amplitudě                | rozmezí 1 dB @ 1 kHz                     |
| Fáze porovnávání                    | 2 ° @ 1 kHz                       |
| Bod akustických přetížení (CHOP)     | \>= 120 dBSPL (nepodporuje = 10 %)          |
| Přenosová rychlost                          | Minimální 24-bit                    |
| Vzorkovací frekvence                     | Minimální kHz 16\*                   |
| Frekvence odpovědi                | rozmezí 3 dB, maska Hz 200 8000 s plovoucí desetinnou čárkou\*|
| Spolehlivost                       | Rozsah velikosti služby Storage teploty ° C-40 až 70 ° C<br />Provozní rozsah teploty ° C-20 55 ° c  |

*\* Vyšší míra vzorkování nebo "širší" frekvence rozsahy může být nutné pro vysoce kvalitní komunikace (VoIP) aplikace*

Výběr vhodné součásti musí být párována s dobrým elektroakustická integrace předejdete tak zhoršení výkonu komponenty používané. Jedinečnými případy použití mohou také vyžadovat další požadavky (například: provoz teploty rozsahy).

## <a name="microphone-array-integration"></a>Pole integrace mikrofonu

Výkon mikrofon pole při integraci do zařízení se bude lišit od specifikace komponenty. Je důležité zajistit, že mikrofon odpovídají i po integraci. Proto měří výkon zařízení po žádné pevné získat nebo EQ by měl splňovat následující doporučení:

|  Parametr        |    Doporučené |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 63 dB (dBSPL signál 94 1 kHz, akustického) |
|  Výstup citlivosti  | -26 dBFS/Pa @ 1 kHz (doporučeno) |
|  Odpovídající amplitudě  | rozmezí 2 dB Hz 200 8000 |
| % NEPODPORUJE\*                 | ≤ % 1, 200-8000 Hz, 94 dBSPL, 5. pořadí |
|  Frekvence odpovědi  | rozmezí 6 dB, maska Hz 200 8000 s plovoucí desetinnou čárkou\*\* |

*\*\* Nízká narušení mluvčího se vyžaduje k měření nepodporuje (např. Neumannova KH120)*

*\*\* "Širší" rozsahy frekvencí může být nutné pro vysoce kvalitní komunikace (VoIP) aplikace*

## <a name="speaker-integration-recommendations"></a>Doporučení pro integraci mluvčího

Je nezbytné pro zařízení se rozpoznávání řeči, které obsahují přednášející zrušení zpětné vazby jsou k dispozici další doporučení pro výběr mluvčího a integrace.

| Parametr                         | Doporučené                       |
|-----------------------------------|-----------------------------------|
| Důležité informace o linearity          | Žádné nelineárních zpracování za odkazem mluvčího, jinak hardwarové zpětné smyčky odkaz datový proud je povinné  |
| Zpětné smyčky mluvčího                  | Prostřednictvím WASAPI, privátní rozhraní API, vlastní ALSA modulu plug-in (Linux), nebo poskytnutý prostřednictvím kanálu firmwaru      |
| % NEPODPORUJE                              | 3\. oktáv minimální 5. pořadí, 70 dBA přehrávání @ 0,8 m ≤ 6.3 %, % 5 315 500 Hz ≤, 630 5000 Hz                 |
| Párování odezvu na mikrofon      | \> dB-10 TCLw metodou ITU-T G.122 přílohy B.4 normalizovány na úrovni mic<br />TCLw = TCLwmeasured \+ (měří úroveň - cílit výstupní citlivost)<br />TCLw = TCLwmeasured \+ (měří úroveň - (-26)) |

## <a name="integration-design-architecture"></a>Architektura návrhu integrace

Následující pokyny pro architekturu jsou nezbytné při integraci mikrofonů zařízení:

| Parametr                         | Doporučení                    |
|-----------------------------------|-----------------------------------|
| MIC Port podobnosti               | Všechny porty mikrofon se stejnou délku jako pole    |
| Dimenze MIC Port               | Port velikost Ø0.8 1.0 mm. Port délka portu prumer / \< 2              |
| Zapečetění MIC                       | Zapečetění gaskets rovnoměrně implementované v zásobníku nahoru. Doporučujeme \> kompresní poměr 70 % maskota gaskets     |
| Spolehlivost MIC                   | Síť by měla být použita k zamezení věci a příchozího přenosu dat (mezi PCB pro dolní přenáší mikrofon a uzavírání těsnění vrchní titulní)  |
| Izolace MIC                     | Pružné gaskets a pronikavost oddělení strukturou, zejména pro izolaci všechny cesty, pronikavost kvůli integrované přednášející      |
| Hodiny vzorkování                    | Zvukové zařízení musí být bez zpoždění a přímé nasazení s nízkou odchylek    |
| Funkce záznamu                 | Zařízení musí být schopen záznamem nezpracované datové proudy jednotlivých kanálů současně |
| USB                               | Všechny zvukové vstupní zařízení USB, musíte nastavit popisovače podle [USB specifikace Rev3 zvukové zařízení](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometrie               | Ovladače musí implementovat [mikrofon pole geometrie popisovače](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) správně  |
| Zjistitelnost                   | Zařízení nesmí mít žádné undiscoverable nebo neovladatelném hardwaru, firmwaru nebo 3. stran softwarových nelineárních zpracování zvuku algoritmy ze zařízení|
| Zachycení formátu                    | Formáty zachycení musí používat minimální vzorkovací frekvenci 16 kHz a doporučené 24-bit hloubky      |

## <a name="electrical-architecture-considerations"></a>Důležité informace o elektrické architektury

Kde je to možné, pole může být připojený k hostiteli USB (jako je například SoC, na kterém běží Microsoft Stack zvuk) a rozhraní pro hlasové služby nebo k jiným aplikacím.

Hardwarové součásti, jako je například převod PDM Pracovník se ujistěte, že dynamických rozsahů a SNR mikrofon je uložena v opakované vzorkovače.

Vysokorychlostní USB zvuku 2.0 třída by měla podporovat v rámci žádné zvukové Mikrokontrolery negace nezbytná šířka pásma pro až sedm kanálů na vyšších vzorkovací frekvence a bitové hloubky.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o zařízení sadou SDK pro řeč](speech-devices-sdk.md)
