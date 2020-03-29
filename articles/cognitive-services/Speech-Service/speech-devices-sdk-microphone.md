---
title: Doporučení pro pole mikrofonu sady SDK pro řeč
titleSuffix: Azure Cognitive Services
description: Doporučení pole mikrofonu SDK pro řečová zařízení. Tyto geometrie pole jsou doporučeny pro použití s Microsoft Audio Stack.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: a87bdd7a55036e8b70f0bc5816d2b587c1569202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168143"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Doporučení pole s mikrofonem s řeči mise SDK

V tomto článku se dozvíte, jak navrhnout pole mikrofonu pro sady SDK řečových zařízení.

Sada Speech Devices SDK funguje nejlépe s mikrofonním polem, které bylo navrženo podle následujících pokynů, včetně geometrie mikrofonu a výběru součástí. Pokyny jsou rovněž uvedeny v otázkách integrace a elektrických úvah.

## <a name="microphone-geometry"></a>Geometrie mikrofonu

Následující geometrie pole jsou doporučeny pro použití s Microsoft Audio Stack. Umístění zdrojů zvuku a odmítnutí okolního hluku je vylepšeno s větším počtem mikrofonů se závislostmi na konkrétních aplikacích, uživatelských scénářích a tvarovém faktoru zařízení.

|     | Kruhové pole |     | Lineární pole |     |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \#Mikrofony | 7 | 4 | 4 | 2 |
| Geometrie | 6 Vnější, 1 střed, Poloměr = 42,5 mm, Rovnoměrně rozložený | 3 Vnější, 1 střed, Poloměr = 42,5 mm, Rovnoměrně rozložený | Délka = 120 mm, rozteč = 40 mm | Rozteč = 40 mm |

Mikrofonní kanály by měly být seřazeny podle číslování znázorněného pro každé výše uvedené pole, které se zvyšuje od 0. Microsoft Audio Stack bude vyžadovat další referenční proud přehrávání zvuku k provedení zrušení ozvěny.

## <a name="component-selection"></a>Výběr komponenty

Součásti mikrofonu by měly být vybrány tak, aby přesně reprodukovaly signál bez šumu a zkreslení.

Doporučené vlastnosti při výběru mikrofonů jsou:

| Parametr | Doporučené |
| --------- | ----------- |
| Snr | \>= 65 dB (signál 1 kHz 94 dBSPL, hluk vážený A) |
| Párování amplitudy | ± 1 dB při 1 kHz |
| Párování fází | ± 2° při 1 kHz |
| Bod akustického přetížení (AOP) | \>= 120 dBSPL (THD = 10 %) |
| Přenosová rychlost | Minimální 24bitový |
| Vzorkovací frekvence | Minimálně 16 kHz\* |
| Frekvenční | ± 3 dB, plovoucí maska 200-8000 Hz\* |
| Spolehlivost | Rozsah skladovací teploty -40°C až 70°C<br />Rozsah provozních teplot -20°C až 55°C |

\*_Vyšší vzorkovací frekvence nebo "širší" frekvenční rozsahy mohou být nezbytné pro vysoce kvalitní komunikační aplikace (VoIP)_

Dobrá volba součásti musí být spárována s dobrou elektroakustickou integrací, aby nedošlo k narušení výkonu použitých součástí. Jedinečné případy použití mohou také vyžadovat další požadavky (například: rozsahy provozních teplot).

## <a name="microphone-array-integration"></a>Integrace mikrofonního pole

Výkon pole mikrofonu při integraci do zařízení se bude lišit od specifikace součásti. Je důležité zajistit, aby mikrofony byly po integraci dobře sladěny. Proto by výkon zařízení měřený po jakémkoli pevném zesílení nebo Ekvalizu ma, měl splňovat následující doporučení:

| Parametr          | Doporučené                                        |
| ------------------ | -------------------------------------------------- |
| Snr                | \>63 dB (signál 1 kHz 94 dBSPL, hluk vážený A) |
| Výstupní citlivost | -26 dBFS/Pa @ 1 kHz (doporučeno)                  |
| Párování amplitudy | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1 %, 200-8000 Hz, 94 dBSPL, 5.             |
| Frekvenční | ± 6 dB, plovoucí maska 200-8000 Hz\*\*              |

\*\*_Pro měření THD je nutný reproduktor s nízkým zkreslením (např._

\*\*_"Širší" frekvenční rozsahy mohou být nezbytné pro vysoce kvalitní komunikační aplikace (VoIP)_

## <a name="speaker-integration-recommendations"></a>Doporučení pro integraci mluvčího

Vzhledem k tomu, že zrušení ozvěny je nezbytné pro zařízení pro rozpoznávání řeči, která obsahují reproduktory, jsou k dispozici další doporučení pro výběr reproduktorů a integraci.

| Parametr | Doporučené |
| --------- | ----------- |
| Důležité informace o linearitě | Žádné nelineární zpracování po odkazu na reproduktor, jinak je vyžadován referenční datový proud zpětné smyčky založený na hardwaru. |
| Zpětná smyčka reproduktoru | Poskytované prostřednictvím WASAPI, privátních rozhraní API, vlastního modulu plug-in ALSA (Linux) nebo prostřednictvím kanálu firmwaru |
| THD% | 3. Oktávová pásma minimálně 5. |
| Echo připojení k mikrofonům | \>-10 dB TCLw metodou ITU-T G.122 podle přílohy B.4 normalizované na úroveň mikrofonu<br />TCLw = TCLwmeasured \+ (měřená úroveň - citlivost cílového výstupu)<br />TCLw = TCLwmeasured \+ (Měřená úroveň - (-26)) |

## <a name="integration-design-architecture"></a>Architektura integračního návrhu

Při integraci mikrofonů do zařízení jsou nezbytné následující pokyny pro architekturu:

| Parametr | Doporučení |
| --------- | -------------- |
| Podobnost portu mikrofonu | Všechny mikrofonní porty mají stejnou délku v poli |
| Rozměry portu mikrofonu | Velikost portu Ø0.8-1.0 mm. Délka portu \< / průměr portu 2 |
| Mic těsnění         | Těsnění rovnoměrně implementována v zásobníku. Doporučujeme \> 70% kompresní poměr pro pěnová těsnění |
| Spolehlivost mikrofonu     | Síť by měla být použita, aby se zabránilo vniknutí prachu a vniknutí (mezi PCB pro spodní portované mikrofony a těsnicí těsnění / horní kryt) |
| Izolace mikrofonu       | Pryžová těsnění a oddělování vibrací skrz strukturu, zejména pro izolaci jakýchkoli vibračních dcest díky integrovaným reproduktorům |
| Vzorkovací hodiny      | Zvuk zařízení musí být bez chvění a výpadků s nízkým posunem |
| Schopnost záznamu   | Zařízení musí být schopno nahrávat jednotlivé kanálové nezpracované proudy současně |
| USB                 | Všechna usb audio vstupní zařízení musí nastavit popisovače podle [USB audio zařízení Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Geometrie mikrofonu | Ovladače musí správně implementovat [popisovače geometrie pole mikrofonu.](https://docs.microsoft.com/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) |
| Zjistitelnost     | Zařízení nesmí mít žádné nezjistitelné nebo nekontrolovatelné hardwarové, firmware nebo nelineární algoritmy zpracování zvuku založené na softwaru třetích stran do zařízení nebo ze zařízení. |
| Formát zachycení      | Formáty sběru musí používat minimální vzorkovací frekvenci 16 kHz a doporučenou 24bitovou hloubku |

## <a name="electrical-architecture-considerations"></a>Důležité informace o elektrické architektuře

V případě potřeby mohou být pole připojena k hostiteli USB (například k soc, který spouští zásobník zvuku společnosti Microsoft) a rozhraní se službami řeči nebo jinými aplikacemi.

Hardwarové součásti, jako je konverze PDM-TDM, by měly zajistit, aby byl dynamický rozsah a SNR mikrofonů zachovány v rámci převzorkovačů.

Vysokorychlostní usb audio třída 2.0 by měla být podporována v rámci všech zvukových MCU, aby byla zajištěna potřebná šířka pásma až pro sedm kanálů při vyšších vzorkovacích frekvencích a bitových hloubkách.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o sadě SDK pro zařízení pro rozpoznávání řeči](speech-devices-sdk.md)
