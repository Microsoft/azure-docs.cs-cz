---
title: Migrace z Zpracování řeči Bingu do služby Speech
titleSuffix: Azure Cognitive Services
description: Naučte se migrovat z existujícího předplatného Zpracování řeči Bingu do služby pro rozpoznávání řeči z Azure Cognitive Services.
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: nitinme
ms.openlocfilehash: 43679c52727f8cc84c7292592b68dddae7f1ea68
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362074"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z Zpracování řeči Bingu do služby pro rozpoznávání řeči

Pomocí tohoto článku můžete migrovat aplikace z rozhraní API pro zpracování řeči Bingu do služby pro rozpoznávání řeči.

Tento článek popisuje rozdíly mezi Zpracování řeči Bingu API a službou pro rozpoznávání řeči a navrhuje strategie pro migraci vašich aplikací. Váš klíč předplatného rozhraní API pro zpracování řeči Bingu nebude spolupracovat se službou Speech. budete potřebovat nové předplatné služby Speech Service.

Jeden klíč předplatného služby Speech uděluje přístup k následujícím funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní hlasy pro převod textu na řeč](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](../translator/translator-info-overview.md))

[Sada Speech SDK](speech-sdk.md) je funkční náhradou pro klientské knihovny zpracování řeči Bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Služba rozpoznávání řeči je převážně podobná Zpracování řeči Bingu, s následujícími rozdíly.

| Příznak | Zpracování řeči Bingu | Služba Speech | Podrobnosti |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje Windows 10, Univerzální platforma Windows (UWP) a .NET Standard 2,0. |
| C++ SDK | : heavy_minus_sign: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje systémy Windows a Linux. |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | Služba rozpoznávání řeči podporuje zařízení s Androidem a řečí. |
| Průběžné rozpoznávání řeči | 10 minut | Neomezeno (se sadou SDK) | Protokoly WebSockets služby Zpracování řeči Bingu a Speech podporují až 10 minut na jedno volání. Sada Speech SDK se ale automaticky znovu připojí při vypršení časového limitu nebo odpojení. |
| Částečné nebo dočasné výsledky | :heavy_check_mark: | :heavy_check_mark: | Pomocí protokolu WebSockets nebo sady SDK. |
| Vlastní modely řeči | :heavy_check_mark: | :heavy_check_mark: | Zpracování řeči Bingu vyžaduje samostatné předplatné Custom Speech. |
| Vlastní hlasová písma | :heavy_check_mark: | :heavy_check_mark: | Zpracování řeči Bingu vyžaduje samostatné vlastní předplatné hlasu. |
| 24 – kHz hlasů | : heavy_minus_sign: | :heavy_check_mark: |
| Rozpoznávání záměrů řeči | Vyžaduje samostatné volání rozhraní LUIS API. | Integrováno (se sadou SDK) | Ke službě Speech můžete použít LUIS klíč. |
| Jednoduché rozpoznávání záměrů | : heavy_minus_sign: | :heavy_check_mark: |
| Dávkové přepisy dlouhých audio souborů | : heavy_minus_sign: | :heavy_check_mark: |
| Režim rozpoznávání | Ruční prostřednictvím identifikátoru URI koncového bodu | Automaticky | Ve službě Speech není dostupný režim rozpoznávání. |
| Prostředí koncového bodu | Globální | Regionální | Místní koncové body zlepšují latenci. |
| Rozhraní REST API | :heavy_check_mark: | :heavy_check_mark: | Rozhraní REST API služby Speech jsou kompatibilní s Zpracování řeči Bingu (jiný koncový bod). Rozhraní REST API podporují funkci převodu textu na řeč a omezené funkce řeči na text. |
| Protokoly WebSockets | :heavy_check_mark: | :heavy_check_mark: | Rozhraní API WebSockets služby Speech je kompatibilní s Zpracování řeči Bingu (jiný koncový bod). Pokud je to možné, migrujte na sadu Speech SDK, abyste zjednodušili kód. |
| Volání rozhraní API služby-služba | :heavy_check_mark: | : heavy_minus_sign: | K dispozici v Zpracování řeči Bingu prostřednictvím knihovny služby C#. |
| Open-Source sada SDK | :heavy_check_mark: | : heavy_minus_sign: |

Služba Speech používá cenový model založený na čase (nikoli model založený na transakcích). Podrobnosti najdete v tématu [ceny služby Speech](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Strategie migrace

Pokud vy nebo vaše organizace máte aplikace ve vývoji nebo v produkčním prostředí, které používají rozhraní API pro zpracování řeči Bingu, měli byste je aktualizovat, aby službu Speech Service co nejdříve používaly. Dostupné sady SDK, ukázky kódu a kurzy najdete v [dokumentaci ke službě Speech](index.yml) .

[Rozhraní REST API](rest-apis.md) služby Speech jsou kompatibilní s rozhraními API zpracování řeči Bingu. Pokud aktuálně používáte Zpracování řeči Bingu rozhraní REST API, stačí změnit jenom koncový bod REST a přepnout na klíč předplatného služby Speech.

Protokoly WebSockets služby Speech Service jsou také kompatibilní s nástroji, které používá Zpracování řeči Bingu. Doporučujeme, abyste pro nový vývoj používali sadu Speech SDK spíše než objekty WebSockets. Je vhodné také migrovat existující kód do sady SDK. Stejně jako u rozhraní REST API ale existující kód, který používá Zpracování řeči Bingu přes objekty WebSockets, vyžaduje pouze změnu v koncovém bodu a aktualizovaný klíč.

Pokud používáte klientskou knihovnu Zpracování řeči Bingu pro konkrétní programovací jazyk, migrace do [sady Speech SDK](speech-sdk.md) vyžaduje změny aplikace, protože rozhraní API se liší. Sada Speech SDK může zjednodušit váš kód a zároveň vám poskytne přístup k novým funkcím. Sada Speech SDK je k dispozici v nejrůznějších programovacích jazycích. Rozhraní API na všech platformách jsou podobná, což usnadňuje vývoj pro více platforem.

Služba rozpoznávání řeči nenabízí globální koncový bod. Zjistěte, jestli vaše aplikace funguje efektivně, když používá jeden místní koncový bod pro veškerý provoz. Pokud ne, použijte geografickou polohu k určení nejefektivnějšího koncového bodu. V každé oblasti, kterou používáte, potřebujete samostatné předplatné služby Speech.

Pokud vaše aplikace používá dlouhotrvající připojení a nemůže použít dostupnou sadu SDK, můžete použít připojení pomocí protokolu WebSockets. Nastavte časový limit 10 minut tím, že se znovu připojíte k odpovídajícím časem.

Začínáme se sadou Speech SDK:

1. Stáhněte si [sadu Speech SDK](speech-sdk.md).
1. Pracujte prostřednictvím průvodců a [kurzů](how-to-recognize-intents-from-speech-csharp.md)pro [rychlý Start](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) služby Speech. Podívejte se také na [ukázky kódu](samples.md) , abyste získali zkušenosti s novými rozhraními API.
1. Aktualizujte aplikaci tak, aby používala službu Speech.

## <a name="support"></a>Podpora

Zákazníci s Zpracování řeči Bingu by měli kontaktovat zákaznickou podporu otevřením [lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Pokud vaše podpora vyžaduje [plán technické podpory](https://azure.microsoft.com/support/plans/), můžete nás taky kontaktovat.

Podporu služby Speech SDK a rozhraní API najdete na [stránce podpory](support.md)služby pro rozpoznávání řeči.

## <a name="next-steps"></a>Další kroky

* [Vyzkoušejte si službu Speech Service zdarma](overview.md#try-the-speech-service-for-free)
* [Rychlý Start: rozpoznávání řeči v aplikaci UWP pomocí sady Speech SDK](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)

## <a name="see-also"></a>Viz také
* [Poznámky k verzi služby Speech Service](releasenotes.md)
* [Co je služba pro rozpoznávání řeči](overview.md)
* [Dokumentace ke službě Speech a sadě Speech SDK](speech-sdk.md#get-the-speech-sdk)
