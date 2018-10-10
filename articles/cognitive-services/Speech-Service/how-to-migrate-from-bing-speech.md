---
title: Migrace z pro zpracování řeči Bingu ve službě řeči
titleSuffix: Azure Cognitive Services
description: Poznejte rozdíly mezi pro zpracování řeči Bingu a Speech Service z pohledu vývojáře a migrujte své aplikace používat službu rozpoznávání řeči.
services: cognitive-services
author: wsturman
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: e72cf547ac911b22a03cae6032351c8c0f22de8e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884845"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z pro zpracování řeči Bingu ve službě řeči

V tomto článku použijte k migraci aplikace z rozhraní API Bingu pro rozpoznávání řeči do Speech Service.

Tento článek popisuje rozdíly mezi rozhraní API pro zpracování řeči Bingu a Speech Service a navrhne strategie pro migraci aplikací. Váš klíč rozhraní API pro zpracování řeči Bingu předplatného nebude změna přijata službou Speech; nové předplatné Speech Service budete potřebovat.

Jeden klíč předplatného Speech Service uděluje přístup k těmto funkcím. Každý se měří zvlášť, takže platíte jenom za funkce, které používáte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní převod textu na řeč hlasů](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [překlady textů](../translator/translator-info-overview.md))

[Sadou SDK pro řeč](speech-sdk.md) funkční nahrazuje klientské knihovny pro zpracování řeči Bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Speech Service je do značné míry na funkci, platformy a programovací jazyk parita se pro zpracování řeči Bingu s těmito rozdíly.

Funkce | Zpracování řeči Bingu | Speech Service | Podrobnosti
-|-|-|-
C++ SDK | : heavy_minus_sign: | : heavy_check_mark: | Speech Service podporuje Windows a Linux
Java SDK | : heavy_check_mark: | : heavy_check_mark: | Speech Service podporuje řeči zařízení s Androidem a
C# SDK | : heavy_check_mark: | : heavy_check_mark: | Speech Service podporuje Windows 10, UPW a .NET Standard 2.0
Rozpoznávání řeči průběžné | 10 minut | Neomezené (pomocí sady SDK) | Pro zpracování řeči Bingu a protokoly Websocket Speech Service podporují až 10 minut na volání. Sadou SDK pro řeč však automaticky znovu připojí na vypršení časového limitu nebo odpojit.
Částečné nebo dočasné výsledky | : heavy_check_mark: | : heavy_check_mark: | Pomocí protokolu Websocket nebo sady SDK
Modely řeči | : heavy_check_mark: | : heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné Custom Speech
Vlastní hlasové písmo | : heavy_check_mark: | : heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné vlastní hlasové
24 kHz hlasů | : heavy_minus_sign: | : heavy_check_mark: 
Rozpoznání záměru řeči | Vyžaduje volání rozhraní API LUIS | Integrováno (SDK) |  Služba LUIS klíč lze použít s Speech Service.
Jednoduché rozpoznání záměru | : heavy_minus_sign: | : heavy_check_mark: 
Batch přepis dlouho zvukové soubory | : heavy_minus_sign: | : heavy_check_mark:
Režim rozpoznávání | Ruční prostřednictvím identifikátor URI koncového bodu | Automaticky | Rozpoznávání režim není k dispozici v Speech Service
Umístění koncového bodu | Globální | Místní | Místní koncové body Zlepšete latenci. Globální koncový bod je v úvahu pro Speech Service.
Rozhraní REST API | : heavy_check_mark: | : heavy_check_mark: | REST API pro rozpoznávání řeči služeb je kompatibilní s pro zpracování řeči Bingu (jiný koncový bod). Rozhraní REST API podporují převod textu na řeč a omezené funkce převodu řeči na text.
Protokoly Websocket | : heavy_check_mark: | : heavy_check_mark: | Speech Service objekty Websocket API je kompatibilní s pro zpracování řeči Bingu (jiný koncový bod). Migrace na SDK řeči, pokud je to možné pro zjednodušení kódu.
Volání rozhraní API Service to service | : heavy_check_mark: | : heavy_minus_sign: | Součástí pro zpracování řeči Bingu prostřednictvím knihovny služby jazyka C#. 
Open source sadu SDK | : heavy_check_mark: | : heavy_minus_sign: |

Speech Service používá cenového modelu založeného na čase (a ne jako model založený na transakce). Zobrazit [Speech Service – ceny](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) podrobnosti.

## <a name="migration-strategies"></a>Strategie migrace

Pokud aplikace v vývojové nebo produkční prostředí, které používají Speech API služby Bing máte vy nebo vaše organizace, měli byste aktualizovat je, aby používaly Speech Service co nejdříve. Najdete v článku [Speech Service dokumentaci](index.yml) dostupných sad SDK, ukázky a kurzy.

Speech Service [rozhraní REST API](rest-apis.md) , musí být kompatibilní s rozhraními API pro zpracování řeči Bingu. Pokud používáte rozhraní REST API pro zpracování řeči Bingu, musíte změnit pouze koncový bod REST a přepněte se na Speech Service klíč předplatného.

Protokoly Websocket služby řeči, budou také kompatibilní s těmi, která používá pro zpracování řeči Bingu. Doporučujeme, abyste vývoj nových projektů cílí na sadu SDK služby řeči, nikoli používá objekty Websocket, a doporučujeme vám k migraci existujícího kódu i v sadě SDK. Nicméně jako pomocí rozhraní REST API existující kód, který se používá pro zpracování řeči Bingu přes WebSockets vyžaduje pouze změny v koncový bod a aktualizovaný klíč.

Pokud používáte klientskou knihovnu pro zpracování řeči Bingu pro konkrétní programovací jazyk, migrace do [sadou SDK pro řeč](speech-sdk.md) budou vyžadovat změny do vaší aplikace, protože rozhraní API se liší. Sadou SDK pro řeč můžete provést kódu jednodušší během také poskytuje přístup k novým funkcím.

V současné době sadou SDK pro řeč podporuje C# (Windows 10 UPW, .NET Standard), Java (zařízení s Androidem a vlastní), Objective C (iOS), C++ (Windows nebo Linuxem) a JavaScript. Rozhraní API na všech platformách jsou podobné, usnadnění vývoj pro víc platforem.

Speech Service aktuálně nenabízí globální koncový bod. Je potřeba určit, pokud vaše aplikace bude fungovat efektivně pomocí jednoho koncového bodu místní pro všechny jeho provozu. Pokud ne, použijte informace o zeměpisné poloze určit nejefektivnější koncový bod. Budete potřebovat samostatné předplatné Speech Service v jednotlivých oblastech, které používáte.

Pokud vaše aplikace používá s dlouhým poločasem rozpadu připojení a nemůžete použít sadu SDK k dispozici, můžete použít připojení WebsSockets a spravovat během 10 minut časový limit znovu připojíte ve vhodných chvílích.

Začínáme se sadou SDK pro řeč:

1. Stáhněte si [řeči SDK](speech-sdk.md).
1. Práce prostřednictvím Speech Service [příručky rychlý úvod](quickstart-csharp-dotnet-windows.md), [kurzy](how-to-recognize-intents-from-speech-csharp.md)a prohlédněte si [ukázky kódu](samples.md) jak získat zkušenosti nová rozhraní API.
1. Aktualizujte aplikace pomocí rozhraní API a Speech service.

## <a name="support"></a>Podpora

Pro zpracování řeči Bingu Zákazníci by tak, že otevřete kontaktujte zákaznickou podporu [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Také nás můžete kontaktovat podporu potřeba vyžaduje-li [plán technické podpory](https://azure.microsoft.com/support/plans/).

Speech Service, sady SDK a rozhraní API podporu, navštivte Speech Service [stránku podpory](support.md).

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu rozpoznávání řeči](get-started.md)
* [Rychlý start: Rozpoznávat řeč v aplikace pro UPW pomocí sady SDK pro řeč](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Zpráva k vydání verze služby řeči](releasenotes.md)
* [Co je Speech service](overview.md)
* [Dokumentace k Speech Service a sady SDK](speech-sdk.md#get-the-sdk)
