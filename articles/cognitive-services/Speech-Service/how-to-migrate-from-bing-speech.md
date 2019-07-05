---
title: Migrace do Azure hlasové služby z pro zpracování řeči Bingu
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat z existujícího předplatného pro zpracování řeči Bingu do hlasové služby Azure.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: gracez
ms.openlocfilehash: 33907437ab330278bdf7b023f6a93bd96e78cbad
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561343"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z pro zpracování řeči Bingu ve službě řeči

V tomto článku použijte k migraci aplikace z rozhraní API Bingu pro rozpoznávání řeči do Speech Service.

Tento článek popisuje rozdíly mezi rozhraní API pro zpracování řeči Bingu a hlasových služeb a navrhne strategie pro migraci aplikací. Váš klíč rozhraní API pro zpracování řeči Bingu předplatného nebude fungovat se službou Speech; budete potřebovat k novému předplatnému služby řeči.

Jeden klíč předplatného hlasové služby uděluje přístup k těmto funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní hlasy pro převod textu na řeč](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](../translator/translator-info-overview.md))

[Sadou SDK pro řeč](speech-sdk.md) funkční nahrazuje klientské knihovny pro zpracování řeči Bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Hlasové služby jsou do značné míry podobná pro zpracování řeči Bingu, s těmito rozdíly.

Funkce | Zpracování řeči Bingu | Hlasové služby | Podrobnosti
-|-|-|-
C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Hlasové služby podporuje Windows a Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Hlasové služby podporuje řeči zařízení s Androidem a.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Hlasové služby podporuje .NET Standard 2.0, Windows 10 a univerzální platformu Windows (UPW).
Rozpoznávání řeči průběžné | 10 minut | Neomezené (pomocí sady SDK) | Pro zpracování řeči Bingu a protokoly Websocket služby řeči podporovat až 10 minut na volání. Sadou SDK pro řeč však automaticky znovu připojí na vypršení časového limitu nebo odpojit.
Částečné nebo dočasné výsledky | :heavy_check_mark: | :heavy_check_mark: | Pomocí protokolu Websocket nebo sady SDK.
Modely řeči | :heavy_check_mark: | :heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné vlastní řeči.
Vlastní hlasové písmo | :heavy_check_mark: | :heavy_check_mark: | Pro zpracování řeči Bingu vyžaduje samostatné předplatné vlastní hlasové.
24 kHz hlasů | : heavy_minus_sign: | :heavy_check_mark:
Rozpoznání záměru řeči | Vyžaduje volání rozhraní API LUIS | Integrováno (SDK) |  LUIS klíč můžete použít se službou Speech.
Jednoduché rozpoznání záměru | : heavy_minus_sign: | :heavy_check_mark:
Batch přepis dlouho zvukové soubory | : heavy_minus_sign: | :heavy_check_mark:
Režim rozpoznávání | Ruční prostřednictvím identifikátor URI koncového bodu | Automatické | Rozpoznávání režim není k dispozici v Speech Service.
Umístění koncového bodu | Globální | Místní | Místní koncové body Zlepšete latenci.
Rozhraní REST API | :heavy_check_mark: | :heavy_check_mark: | Rozhraní REST API pro rozpoznávání řeči služby jsou kompatibilní se pro zpracování řeči Bingu (jiný koncový bod). Rozhraní REST API podporují převod textu na řeč a omezené funkce převodu řeči na text.
Protokoly Websocket | :heavy_check_mark: | :heavy_check_mark: | Rozhraní Speech API služby objekty Websocket je kompatibilní s pro zpracování řeči Bingu (jiný koncový bod). Migrace do sady SDK k rozpoznávání řeči, pokud je to možné, pro zjednodušení kódu.
Volání rozhraní API Service to service | :heavy_check_mark: | : heavy_minus_sign: | Součástí pro zpracování řeči Bingu prostřednictvím knihovny služby jazyka C#.
Open source sadu SDK | :heavy_check_mark: | : heavy_minus_sign: |

Hlasové služby použijte cenového modelu založeného na čase (a ne jako model založený na transakce). Zobrazit [ceny za hlasové služby](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) podrobnosti.

## <a name="migration-strategies"></a>Strategie migrace

Pokud aplikace v vývojové nebo produkční prostředí, které používají Speech API služby Bing máte vy nebo vaše organizace, měli byste aktualizovat je, aby používaly hlasové služby co nejdříve. Najdete v článku [hlasové služby dokumentaci](index.yml) dostupných sad SDK, ukázky a kurzy.

Hlasové služby [rozhraní REST API](rest-apis.md) , musí být kompatibilní s rozhraními API pro zpracování řeči Bingu. Pokud aktuálně používáte rozhraní REST API pro zpracování řeči Bingu, musíte změnit pouze koncový bod REST a přepněte se na klíč předplatného hlasové služby.

Protokoly Websocket služby řeči, budou také kompatibilní s těmi, která používá pro zpracování řeči Bingu. Doporučujeme, abyste pro vývoj nových projektů, použijte sadou SDK pro řeč místo objekty Websocket. Je vhodné k migraci existujícího kódu i v sadě SDK. Nicméně jako pomocí rozhraní REST API existující kód, který se používá pro zpracování řeči Bingu přes WebSockets vyžaduje pouze změny v koncový bod a aktualizovaný klíč.

Pokud používáte klientskou knihovnu pro zpracování řeči Bingu pro konkrétní programovací jazyk, migrace do [sadou SDK pro řeč](speech-sdk.md) vyžaduje změny aplikace, protože rozhraní API se liší. Sadou SDK pro řeč mohli kódu jednodušší, při také poskytuje přístup k novým funkcím.

V současné době podporuje sadou SDK pro řeč C# ([podrobnosti tady](https://aka.ms/csspeech)), Java (zařízení s Androidem a vlastní), Objective C (iOS), C++ (Windows nebo Linuxem) a JavaScript. Rozhraní API na všech platformách jsou podobné, usnadnění vývoj pro víc platforem.

Hlasové služby nenabízí globální koncový bod. Určete, pokud funkce aplikace efektivně pokud používá jeden místní koncový bod pro všechny jeho provozu. Pokud ne, použijte informace o zeměpisné poloze určit nejefektivnější koncový bod. Budete potřebovat samostatné předplatné hlasové služby v jednotlivých oblastech, které používáte.

Pokud vaše aplikace používá s dlouhým poločasem rozpadu připojení a nemůžete použít sadu SDK k dispozici, můžete použít objekty Websocket připojení. Spravujte během 10 minut časový limit připojení ve vhodných chvílích.

Začínáme se sadou SDK pro řeč:

1. Stáhněte si [řeči SDK](speech-sdk.md).
1. Práce prostřednictvím hlasové služby [příručky rychlý úvod](quickstart-csharp-dotnet-windows.md) a [kurzy](how-to-recognize-intents-from-speech-csharp.md). Si také prohlédnout [ukázky kódu](samples.md) jak získat zkušenosti nová rozhraní API.
1. Aktualizace aplikace pro používání hlasové služby.

## <a name="support"></a>Podpora

Pro zpracování řeči Bingu Zákazníci by tak, že otevřete kontaktujte zákaznickou podporu [lístek podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Také nás můžete kontaktovat podporu potřeba vyžaduje-li [plán technické podpory](https://azure.microsoft.com/support/plans/).

Speech Service, sady SDK a rozhraní API podporu, navštivte hlasové služby [stránku podpory](support.md).

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte hlasové služby zdarma](get-started.md)
* [Rychlé zprovoznění: Rozpoznávání řeči do aplikace pro UPW pomocí sady SDK pro řeč](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Zpráva k vydání verze hlasové služby](releasenotes.md)
* [Co je Speech Service](overview.md)
* [Dokumentace k hlasových služeb a sadou SDK pro řeč](speech-sdk.md#get-the-sdk)
