---
title: Migrace ze služby Proslov Bing do řeči
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak migrovat z existujícího předplatného řeči Bingu na službu Speech ze služby Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 7b78bdb070cdf1364fe7fbdc75f175be7ce145ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656452"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z řeči Bingu do služby Řeč

Tento článek slouží k migraci aplikací z rozhraní API pro rozpoznávání řeči Bingu do služby Řeč.

Tento článek popisuje rozdíly mezi disky Windows pro rozpoznávání řeči Bingu a službou Rozpoznávání řeči a navrhuje strategie pro migraci aplikací. Váš klíč předplatného rozhraní API pro řeč Bing nebude fungovat se službou Řeč. budete potřebovat nové předplatné služby Speech.

Jeden klíč předplatného služby Speech uděluje přístup k následujícím funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní hlasy pro převod textu na řeč](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](../translator/translator-info-overview.md))

Sada [Speech SDK](speech-sdk.md) je funkční náhradou pro klientské knihovny hlasové ho bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Služba Řeč je do značné míry podobná službě Bing Speech s následujícími rozdíly.

| Funkce | Zpracování řeči Bingu | Služba Speech | Podrobnosti |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje windows 10, univerzální platformu Windows (UPW) a rozhraní .NET Standard 2.0. |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje Windows a Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje zařízení android a řeč. |
| Nepřetržité rozpoznávání řeči | 10 minut | Neomezený (s SDK) | Protokoly WebSockets služby Bing pro řeč i řeč podporují až 10 minut na volání. Sada Speech SDK se však automaticky znovu připojí v časovém nebo odpojení. |
| Částečné nebo průběžné výsledky | :heavy_check_mark: | :heavy_check_mark: | S protokolem WebSockets nebo sadou SDK. |
| Vlastní modely řeči | :heavy_check_mark: | :heavy_check_mark: | Funkce Proslov Bing vyžaduje samostatné vlastní předplatné řeči. |
| Vlastní hlasová písma | :heavy_check_mark: | :heavy_check_mark: | Funkce Proslov Bing vyžaduje samostatné vlastní hlasové předplatné. |
| 24kHz hlasy | :heavy_minus_sign: | :heavy_check_mark: |
| Rozpoznávání záměru řeči | Vyžaduje samostatné volání rozhraní LUIS API. | Integrovaná (s Sadou SDK) | Můžete použít klíč LUIS se službou Řeč. |
| Jednoduché rozpoznávání záměru | :heavy_minus_sign: | :heavy_check_mark: |
| Dávkový přepis dlouhých zvukových souborů | :heavy_minus_sign: | :heavy_check_mark: |
| Režim rozpoznávání | Ruční identifikátor URI přes koncový bod | Automaticky | Režim rozpoznávání není ve službě Řeč k dispozici. |
| Lokalita koncového bodu | Globální | Oblastní | Místní koncové body zlepšují latenci. |
| Rozhraní REST API | :heavy_check_mark: | :heavy_check_mark: | Rozhraní REST služby Rebě relací jsou kompatibilní s funkcemi Bing Speech (jiný koncový bod). Rest API podporují převod textu na řeč a omezené funkce převodu řeči na text. |
| Protokoly WebSockets | :heavy_check_mark: | :heavy_check_mark: | Rozhraní API služby WebSockets služby Speech je kompatibilní s projevem Bingu (jiný koncový bod). Pokud je to možné, migrujte do sady Speech SDK, abyste zjednodušili kód. |
| Volání rozhraní API služby a služby | :heavy_check_mark: | :heavy_minus_sign: | K dispozici v řeči Bing prostřednictvím knihovny služeb C#. |
| Sada SDK s otevřeným zdrojovým kódem | :heavy_check_mark: | :heavy_minus_sign: |

Služba Řeč používá cenový model založený na čase (nikoli model založený na transakcích). Podrobnosti najdete v [tématu Ceny služby Řeči.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="migration-strategies"></a>Strategie migrace

Pokud vy nebo vaše organizace mají aplikace ve vývoji nebo produkčním prostředí, které používají rozhraní API pro rozpoznávání řeči Bingu, měli byste je co nejdříve aktualizovat, abyste mohli používat službu Řeč. Informace o dostupných sadách SDK, ukázkách kódu a kurzech naleznete v [dokumentaci ke službě Řeč.](index.yml)

Rozhraní REST služby [Ret](rest-apis.md) jsou kompatibilní s rozhraními API pro rozpoznávání řeči Bingu. Pokud aktuálně používáte api služby Bing Řeči REST, stačí změnit koncový bod REST a přepnout na klíč předplatného služby Speech.

Protokoly WebSockets služby Speech jsou také kompatibilní s protokoly používanými službou Bing Speech. Doporučujeme pro nový vývoj použít sady Speech SDK spíše než WebSockets. Je vhodné migrovat existující kód do sady SDK také. Však stejně jako u rozhraní REST API, existující kód, který používá bing řeči přes WebSockets vyžaduje pouze změnu koncového bodu a aktualizovaný klíč.

Pokud používáte knihovnu klienta Pro slovovou službu Bing pro konkrétní programovací jazyk, migrace do [sady Speech SDK](speech-sdk.md) vyžaduje změny vaší aplikace, protože rozhraní API se liší. Sada Speech SDK může usnadnit váš kód a zároveň vám poskytne přístup k novým funkcím. Sada Speech SDK je k dispozici v široké škále programovacích jazyků. Api na všech platformách jsou podobné, což usnadňuje vývoj více platforem.

Služba Řeč nenabízí globální koncový bod. Zjistěte, zda vaše aplikace funguje efektivně, když používá jeden místní koncový bod pro všechny jeho provoz. Pokud ne, použijte geolokaci k určení nejúčinnějšího koncového bodu. Potřebujete samostatné předplatné služby Rozpoznávání řeči v každé oblasti, kterou používáte.

Pokud vaše aplikace používá dlouhotrvající připojení a nelze použít k dispozici SDK, můžete použít připojení WebSockets. Spravujte limit 10minutového časového limitu opětovným připojením ve vhodnou dobu.

Jak začít s sadou Speech SDK:

1. Stáhněte si [sadu Speech SDK](speech-sdk.md).
1. Práce prostřednictvím služby Speech [service quickstart návody](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) a [kurzy](how-to-recognize-intents-from-speech-csharp.md). Podívejte se také na [ukázky kódu](samples.md) získat zkušenosti s novými api.
1. Aktualizujte aplikaci tak, aby používala službu Řeč.

## <a name="support"></a>Podpora

Zákazníci služby Bing Speech by měli kontaktovat zákaznickou podporu otevřením [lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Můžete nás také kontaktovat, pokud vaše potřeba podpory vyžaduje [plán technické podpory](https://azure.microsoft.com/support/plans/).

Pro službu Rozpoznávání řeči, sadu SDK a podporu rozhraní API navštivte [stránku podpory](support.md)služby Řeč .

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte službu Speech zdarma](get-started.md)
* [Úvodní příručka: Rozpoznávání řeči v aplikaci UPW pomocí sady Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Viz také
* [Poznámky k verzi služby rozpoznávání řeči](releasenotes.md)
* [Co je služba Řeč](overview.md)
* [Dokumentace služby rozpoznávání řeči a sady SDK](speech-sdk.md#get-the-speech-sdk)
