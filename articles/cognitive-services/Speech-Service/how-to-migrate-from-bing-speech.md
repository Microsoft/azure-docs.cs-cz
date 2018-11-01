---
title: Migrace z pro zpracování řeči Bingu ve službě řeči
titleSuffix: Azure Cognitive Services
description: Poznejte rozdíly mezi pro zpracování řeči Bingu a Speech Service z pohledu vývojáře a migrujte své aplikace používat službu rozpoznávání řeči.
services: cognitive-services
author: wsturman
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: fdd22e14e0b7636dbc337a20dd69bf93696bb924
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50416276"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z pro zpracování řeči Bingu ve službě řeči

V tomto článku použijte k migraci aplikace z rozhraní API Bingu pro rozpoznávání řeči do Speech Service.

Tento článek popisuje rozdíly mezi rozhraní API pro zpracování řeči Bingu a Speech Service a navrhne strategie pro migraci aplikací. Váš klíč rozhraní API pro zpracování řeči Bingu předplatného nebude změna přijata službou Speech; nové předplatné Speech Service budete potřebovat.

Jediný klíč předplatného služby Speech poskytuje přístup k následujícím funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní hlasy pro převod textu na řeč](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](../translator/translator-info-overview.md))

[Sadou SDK pro řeč](speech-sdk.md) funkční nahrazuje klientské knihovny pro zpracování řeči Bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Speech Service je do značné míry podobná pro zpracování řeči Bingu, s těmito rozdíly.

Funkce | Zpracování řeči Bingu | Speech Service | Podrobnosti
-|-|-|-
C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Speech Service podporuje Windows a Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service podporuje řeči zařízení s Androidem a.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Speech Service podporuje .NET Standard 2.0, Windows 10 a univerzální platformu Windows (UPW).
Rozpoznávání řeči průběžné | 10 minut | Neomezené (pomocí sady SDK) | Pro zpracování řeči Bingu a protokoly Websocket Speech Service podporují až 10 minut na volání. Sadou SDK pro řeč však automaticky znovu připojí na vypršení časového limitu nebo odpojit.
Částečné nebo dočasné výsledky | :heavy_check_mark: | :heavy_check_mark: | Pomocí protokolu Websocket nebo sady SDK.
Modely řeči | :heavy_check_mark: | :heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné vlastní řeči.
Vlastní hlasové písmo | :heavy_check_mark: | :heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné vlastní hlasové.
24 kHz hlasů | : heavy_minus_sign: | :heavy_check_mark: 
Rozpoznání záměru řeči | Vyžaduje volání rozhraní API LUIS | Integrováno (SDK) |  LUIS klíč můžete použít se službou Speech.
Jednoduché rozpoznání záměru | : heavy_minus_sign: | :heavy_check_mark: 
Batch přepis dlouho zvukové soubory | : heavy_minus_sign: | :heavy_check_mark:
Režim rozpoznávání | Ruční prostřednictvím identifikátor URI koncového bodu | Automaticky | Rozpoznávání režim není k dispozici v Speech Service.
Umístění koncového bodu | Globální | Místní | Místní koncové body Zlepšete latenci.
Rozhraní REST API | :heavy_check_mark: | :heavy_check_mark: | REST API pro rozpoznávání řeči služeb je kompatibilní s pro zpracování řeči Bingu (jiný koncový bod). Rozhraní REST API podporují převod textu na řeč a omezené funkce převodu řeči na text.
Protokoly Websocket | :heavy_check_mark: | :heavy_check_mark: | Speech Service objekty Websocket API je kompatibilní s pro zpracování řeči Bingu (jiný koncový bod). Migrace do sady SDK k rozpoznávání řeči, pokud je to možné, pro zjednodušení kódu.
Volání rozhraní API Service to service | :heavy_check_mark: | : heavy_minus_sign: | Součástí pro zpracování řeči Bingu prostřednictvím knihovny služby jazyka C#. 
Open source sadu SDK | :heavy_check_mark: | : heavy_minus_sign: |

Speech Service používá cenového modelu založeného na čase (a ne jako model založený na transakce). Podrobnosti najdete na stránce s [cenami za službu Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="migration-strategies"></a>Strategie migrace

Pokud aplikace v vývojové nebo produkční prostředí, které používají Speech API služby Bing máte vy nebo vaše organizace, měli byste aktualizovat je, aby používaly Speech Service co nejdříve. Najdete v článku [Speech Service dokumentaci](index.yml) dostupných sad SDK, ukázky a kurzy.

Speech Service [rozhraní REST API](rest-apis.md) , musí být kompatibilní s rozhraními API pro zpracování řeči Bingu. Pokud aktuálně používáte rozhraní REST API pro zpracování řeči Bingu, budete potřebovat pouze změna koncového bodu REST a přepnout na Speech Service klíč předplatného.

Protokoly Websocket služby řeči, budou také kompatibilní s těmi, která používá pro zpracování řeči Bingu. Doporučujeme, abyste pro vývoj nových projektů, použijte sadu SDK služby pro zpracování řeči raději objekty Websocket. Je vhodné k migraci existujícího kódu i v sadě SDK. Nicméně jako pomocí rozhraní REST API existující kód, který se používá pro zpracování řeči Bingu přes WebSockets vyžaduje pouze změny v koncový bod a aktualizovaný klíč.

Pokud používáte klientskou knihovnu pro zpracování řeči Bingu pro konkrétní programovací jazyk, migrace do [sadou SDK pro řeč](speech-sdk.md) vyžaduje změny aplikace, protože rozhraní API se liší. Sadou SDK pro řeč mohli kódu jednodušší, při také poskytuje přístup k novým funkcím.

V současné době sadou SDK pro řeč podporuje C# (Windows 10 UPW, .NET Standard), Java (zařízení s Androidem a vlastní), Objective C (iOS), C++ (Windows nebo Linuxem) a JavaScript. Rozhraní API na všech platformách jsou podobné, usnadnění vývoj pro víc platforem.

Speech Service aktuálně nenabízí globální koncový bod. Určete, pokud funkce aplikace efektivně pokud používá jeden místní koncový bod pro všechny jeho provozu. Pokud ne, použijte informace o zeměpisné poloze určit nejefektivnější koncový bod. Budete potřebovat samostatné předplatné Speech Service v jednotlivých oblastech, které používáte.

Pokud vaše aplikace používá s dlouhým poločasem rozpadu připojení a nemůžete použít sadu SDK k dispozici, můžete použít objekty Websocket připojení. Spravujte během 10 minut časový limit připojení ve vhodných chvílích.

Začínáme se sadou SDK pro řeč:

1. Stáhněte si [řeči SDK](speech-sdk.md).
1. Práce prostřednictvím Speech Service [příručky rychlý úvod](quickstart-csharp-dotnet-windows.md) a [kurzy](how-to-recognize-intents-from-speech-csharp.md). Si také prohlédnout [ukázky kódu](samples.md) jak získat zkušenosti nová rozhraní API.
1. Aktualizujte aplikace pomocí rozhraní API a Speech Service.

## <a name="support"></a>Podpora

Pro zpracování řeči Bingu Zákazníci by tak, že otevřete kontaktujte zákaznickou podporu [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Také nás můžete kontaktovat podporu potřeba vyžaduje-li [plán technické podpory](https://azure.microsoft.com/support/plans/).

Speech Service, sady SDK a rozhraní API podporu, navštivte Speech Service [stránku podpory](support.md).

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte zdarma službu rozpoznávání řeči](get-started.md)
* [Rychlý start: Rozpoznávat řeč v aplikace pro UPW pomocí sady SDK pro řeč](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Zpráva k vydání verze služby řeči](releasenotes.md)
* [Co je Speech Service](overview.md)
* [Dokumentace k Speech Service a sady SDK](speech-sdk.md#get-the-sdk)
