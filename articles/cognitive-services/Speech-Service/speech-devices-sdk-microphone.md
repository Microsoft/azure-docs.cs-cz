---
title: Doporučení pro pole mikrofonu v sadě Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK doporučení pole mikrofonu. Tato pole geometrií se doporučují pro použití s Microsoft audio stackem.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015337"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Doporučení pro pole mikrofonu v sadě Speech Devices SDK

V tomto článku se dozvíte, jak navrhnout pole mikrofonu pro sadu Speech Devices SDK.

Sada Speech Devices SDK nejlépe funguje s polem mikrofonu navrženým podle následujících pokynů, včetně geometrie mikrofonu a výběru komponent. Doprovodné materiály jsou také k disfaktorům pro integraci a elektroinstalace.

## <a name="microphone-geometry"></a>Geometrie mikrofonu

Pro použití s Microsoft audio stackem se doporučuje následující geometrií pole. Umístění zvukových zdrojů a odmítnutí okolního hluku je vylepšeno s větším počtem mikrotelefonů se závislostmi na konkrétních aplikacích, uživatelských scénářích a faktoru formuláře zařízení.

| MICS & geometrie | Kruhové pole | Kruhové pole | Lineární pole | Lineární pole |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| Geometrie | 6 vnější, 1 střed, poloměr = 42,5 mm, rovnoměrně rozmístěný | 3 vnější, 1 střed, poloměr = 42,5 mm, rovnoměrně rozmístěný | Délka = 120 mm, mezery = 40 mm | Mezery = 40 mm |

Kanály mikrofonu by se měly seřadit podle číslování zobrazeného pro každé pole, které se zvýší z 0. Aby bylo možné provést zrušení ozvěny, bude zvukový zásobník Microsoftu vyžadovat další referenční datový proud zvukového přehrávání.

## <a name="component-selection"></a>Výběr součásti

Pro přesný reprodukování signálu bez rušivého hluku a narušení by měly být vybrány součásti mikrofonu.

Doporučené vlastnosti při výběru mikrofonu jsou:

| Parametr | Doporučeno |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz signálu 94 dBSPL, s váženým hlukem) |
| Porovnání amplitudy | ± 1 dB @ 1 kHz |
| Porovnání fáze | ± 2 ° @ 1 kHz |
| Akustický bod přetížení (AOP) | \>= 120 dBSPL (THD = 10%) |
| Přenosová rychlost | Minimální verze 24 bitů |
| Vzorkovací frekvence | Minimální 16 kHz\* |
| Frekvence – odezva | ± 3 dB, 200-8000 Hz, plovoucí maska\* |
| Spolehlivost | Rozsah teploty úložiště – 40 °C až 70 oC<br />Rozsah provozních teplot – 20 oC až 55 °C |

\*_Pro aplikace VoIP (High Quality Communication) můžou být potřeba vyšší vzorkovací frekvence nebo širší rozsah frekvencí._

Dobrým výběrem komponenty musí být párována s dobrou integrací electroacoustic, aby nedošlo k narušení výkonu používaných komponent. Jedinečné případy použití mohou také vyžadovat další požadavky (například: rozsahy provozních teplot).

## <a name="microphone-array-integration"></a>Integrace pole mikrofonu

Výkon pole mikrofonu, když se integruje do zařízení, se liší od specifikace součásti. Je důležité zajistit, aby se mikrofony po integraci dobře shodovaly. Proto by výkon zařízení měřený po jakémkoli dlouhodobém získání nebo EQ měl splňovat následující doporučení:

| Parametr          | Doporučeno                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz signálu 94 dBSPL, s váženým hlukem) |
| Citlivost výstupu | -26 dBFS/PA @ 1 kHz (doporučeno)                  |
| Porovnání amplitudy | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5.5. obj.             |
| Frekvence – odezva | ± 6 dB, plovoucí maska 200-8000 Hz\*\*              |

\*\*_K měření THD se vyžaduje reproduktor s malým zkreslením (např. Neumann KH120)._

\*\*_Pro aplikace VoIP (High Quality Communication) můžou být nezbytné rozsahy frekvencí "širší"._

## <a name="speaker-integration-recommendations"></a>Doporučení pro integraci mluvčího

Pro zařízení rozpoznávání řeči, která obsahují reproduktory, je třeba zrušení ozvěny k dispozici pro výběr mluvčího a integraci dalších doporučení.

| Parametr | Doporučeno |
| --------- | ----------- |
| Otázky linearity | Žádné nelineární zpracování po odkazech na mluvčí, jinak se vyžaduje hardwarový Stream reference zpětné smyčky. |
| Smyčka mluvčího | Poskytuje se prostřednictvím WASAPI, privátních rozhraní API, vlastního modulu plug-in ALSA (Linux) nebo poskytovaných prostřednictvím kanálu firmwaru. |
| THD% | 3 Octave pásma minimální než 5.70 dBA, dBA přehrávání @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Navýšení vazby na mikrofony | \> – 10 dB TCLw pomocí ITU-T G. 122 – metoda normalizovaná na úroveň MIC<br />TCLw = TCLwmeasured \+ (měřená citlivost výstupu cíle na úrovni)<br />TCLw = TCLwmeasured \+ (měřená úroveň-(-26)) |

## <a name="integration-design-architecture"></a>Architektura návrhu integrace

Při integraci mikrotelefonů do zařízení jsou nutné následující pokyny pro architekturu:

| Parametr | Doporučení |
| --------- | -------------- |
| Podobnost portů na mikrofonu | Všechny porty mikrofonu mají v poli stejnou délku. |
| Rozměry portů MIC | Velikost portu ř. 0,8 – 1,0 mm. Délka portu/průměr portu \< 2 |
| Zapečetění mikrofonu         | Zapečetění gaskets se stejnoměrně implementuje v zásobníku. Doporučuje \> 70% kompresní poměr pro pěnu gaskets |
| Spolehlivost mikrofonu     | Síť by se měla použít k zamezení prachu a vstupu (mezi PCB pro nejnižší přenosová čísla a uzavírání Gasket/horního krytu). |
| Izolace mikrofonu       | Pryžové gaskets a vibrace odpojuje se strukturou, zejména pro izolaci všech cest vibrací z důvodu integrovaných mluvčích. |
| Vzorkování – hodiny      | Zvuk zařízení musí být bez kolísání a poklesu s nízkým posunem. |
| Možnost záznamu   | Zařízení musí být schopné nahrávat nezpracované proudy jednotlivých kanálů současně. |
| USB                 | Všechna zařízení se zvukovým vstupem USB musí nastavit popisovače podle [specifikace USB audio Devices Rev3](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) . |
| Geometrie mikrofonu | Ovladače musí správně implementovat [popisovače geometrie pro pole mikrofonu](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) . |
| Zjistitelnost     | Zařízení nesmí mít žádný z nezjištěného nebo neovladatelného hardwaru, firmwaru nebo softwaru od jiných výrobců, než je nelineární zpracování zvuku, do/ze zařízení. |
| Formát zachycení      | Formáty zachycení musí používat minimální vzorkovací frekvenci o 16 kHz a doporučenou 16bitovou hloubku. |

## <a name="electrical-architecture-considerations"></a>Předpoklady elektrické architektury

V případě potřeby můžou být pole připojená k hostiteli USB (například SoC, který spouští Microsoft audio Stack) a rozhraní pro služby řeči nebo jiné aplikace.

Hardwarové součásti, jako je třeba konverze PDM-to-TDM, by měly zajistit, aby se dynamický rozsah a SNR mikrotelefonů zachovaly v rámci opakovaných vzorkovačů.

Vysoká rychlost zvukového rozhraní USB 2,0 by měla být podporovaná v jakémkoli zvukovém mikrokontrolery, aby se zajistila potřebná šířka pásma až pro sedm kanálů s vyššími vzorkovacími sazbami a bitovými hloubkami.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o sadě Speech SDK pro zařízení](speech-devices-sdk.md)