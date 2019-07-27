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
ms.date: 10/01/2018
ms.author: nitinme
ms.openlocfilehash: 5694894a78a46ad658ec18f210c6a82fb82df23f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559599"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>Migrace z Zpracování řeči Bingu do služby pro rozpoznávání řeči

Pomocí tohoto článku můžete migrovat aplikace z rozhraní API pro zpracování řeči Bingu do služby pro rozpoznávání řeči.

Tento článek popisuje rozdíly mezi Zpracování řeči Bingu API a službami pro rozpoznávání řeči a navrhuje strategie pro migraci vašich aplikací. Váš klíč předplatného rozhraní API pro zpracování řeči Bingu nebude spolupracovat se službou Speech. budete potřebovat nové předplatné služby Speech Services.

Jeden klíč předplatného služby Speech Services uděluje přístup k následujícím funkcím. Každá z nich se měří zvlášť, takže se vám účtují pouze funkce, které využíváte.

* [Převod řeči na text](speech-to-text.md)
* [Vlastní převod řeči na text](https://cris.ai)
* [Převod textu na řeč](text-to-speech.md)
* [Vlastní hlasy pro převod textu na řeč](how-to-customize-voice-font.md)
* [Překlad řeči](speech-translation.md) (nezahrnuje [Překlad textu](../translator/translator-info-overview.md))

[Sada Speech SDK](speech-sdk.md) je funkční náhradou pro klientské knihovny zpracování řeči Bingu, ale používá jiné rozhraní API.

## <a name="comparison-of-features"></a>Porovnání funkcí

Služba Speech Services je převážně podobná Zpracování řeči Bingu, s následujícími rozdíly.

Funkce | Zpracování řeči Bingu | Hlasové služby | Podrobnosti
-|-|-|-
C++SADĚ | : heavy_minus_sign: | :heavy_check_mark: | Služba Speech Services podporuje systémy Windows a Linux.
Java SDK | :heavy_check_mark: | :heavy_check_mark: | Služba Speech Services podporuje zařízení s Androidem a řečí.
C# SDK | :heavy_check_mark: | :heavy_check_mark: | Služba Speech Services podporuje Windows 10, Univerzální platforma Windows (UWP) a .NET Standard 2,0.
Průběžné rozpoznávání řeči | 10 minut | Neomezeno (se sadou SDK) | Protokoly WebSockets služby Zpracování řeči Bingu a Speech podporují až 10 minut na jedno volání. Sada Speech SDK se ale automaticky znovu připojí při vypršení časového limitu nebo odpojení.
Částečné nebo dočasné výsledky | :heavy_check_mark: | :heavy_check_mark: | Pomocí protokolu WebSockets nebo sady SDK.
Vlastní modely řeči | :heavy_check_mark: | :heavy_check_mark: | Zpracování řeči Bingu vyžaduje samostatné předplatné Custom Speech.
Vlastní hlasová písma | :heavy_check_mark: | :heavy_check_mark: | Zpracování řeči Bingu vyžaduje samostatné vlastní předplatné hlasu.
24 – KHz hlasů | : heavy_minus_sign: | :heavy_check_mark:
Rozpoznání záměru řeči | Vyžaduje samostatné volání rozhraní LUIS API. | Integrováno (se sadou SDK) |  Ke službě Speech můžete použít LUIS klíč.
Jednoduché rozpoznávání záměrů | : heavy_minus_sign: | :heavy_check_mark:
Dávkové přepisy dlouhých audio souborů | : heavy_minus_sign: | :heavy_check_mark:
Režim rozpoznávání | Ruční prostřednictvím identifikátoru URI koncového bodu | Automatické | Režim rozpoznávání není v hlasové službě k dispozici.
Prostředí koncového bodu | Globální | Oblast | Místní koncové body zlepšují latenci.
Rozhraní REST API | :heavy_check_mark: | :heavy_check_mark: | Rozhraní REST API služby Speech Services jsou kompatibilní s Zpracování řeči Bingu (jiný koncový bod). Rozhraní REST API podporují funkci převodu textu na řeč a omezené funkce řeči na text.
Protokoly WebSockets | :heavy_check_mark: | :heavy_check_mark: | Rozhraní API WebSockets služby Speech Services je kompatibilní s Zpracování řeči Bingu (jiný koncový bod). Pokud je to možné, migrujte na sadu Speech SDK, abyste zjednodušili kód.
Volání rozhraní API služby-služba | :heavy_check_mark: | : heavy_minus_sign: | K dispozici v Zpracování řeči Bingu C# prostřednictvím knihovny služeb.
Open Source sada SDK | :heavy_check_mark: | : heavy_minus_sign: |

Služba Speech Services používá cenový model založený na čase (nikoli model založený na transakcích). Podrobnosti najdete v tématu [ceny služby Speech Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) .

## <a name="migration-strategies"></a>Strategie migrace

Pokud vy nebo vaše organizace máte aplikace ve vývoji nebo v produkčním prostředí, které používají rozhraní API pro zpracování řeči Bingu, měli byste je podle potřeby aktualizovat, aby používaly hlasové služby. Dostupné sady SDK, ukázky kódu a kurzy najdete v [dokumentaci ke službám Speech](index.yml) .

[Rozhraní REST API](rest-apis.md) služby Speech Services jsou kompatibilní s rozhraními API zpracování řeči Bingu. Pokud aktuálně používáte Zpracování řeči Bingu rozhraní REST API, stačí změnit jenom koncový bod REST a přepnout na klíč předplatného služby Speech Services.

Protokoly WebSockets služby Speech Services jsou taky kompatibilní s nástroji, které používá Zpracování řeči Bingu. Doporučujeme, abyste pro nový vývoj používali sadu Speech SDK spíše než objekty WebSockets. Je vhodné také migrovat existující kód do sady SDK. Stejně jako u rozhraní REST API ale existující kód, který používá Zpracování řeči Bingu přes objekty WebSockets, vyžaduje pouze změnu v koncovém bodu a aktualizovaný klíč.

Pokud používáte klientskou knihovnu Zpracování řeči Bingu pro konkrétní programovací jazyk, migrace do [sady Speech SDK](speech-sdk.md) vyžaduje změny aplikace, protože rozhraní API se liší. Sada Speech SDK může zjednodušit váš kód a zároveň vám poskytne přístup k novým funkcím.

Sada Speech SDK v současné době C# podporuje ([Podrobnosti zde](https://aka.ms/csspeech)), Java (Android a vlastní zařízení), cíl C (iOS) C++ , (Windows a Linux) a JavaScript. Rozhraní API na všech platformách jsou podobná, což usnadňuje vývoj pro více platforem.

Služba Speech Services nenabízí globální koncový bod. Zjistěte, jestli vaše aplikace funguje efektivně, když používá jeden místní koncový bod pro veškerý provoz. Pokud ne, použijte geografickou polohu k určení nejefektivnějšího koncového bodu. V každé oblasti, kterou používáte, potřebujete samostatné předplatné služby Speech Services.

Pokud vaše aplikace používá dlouhotrvající připojení a nemůže použít dostupnou sadu SDK, můžete použít připojení pomocí protokolu WebSockets. Nastavte časový limit 10 minut tím, že se znovu připojíte k odpovídajícím časem.

Začínáme se sadou Speech SDK:

1. Stáhněte si [řeči SDK](speech-sdk.md).
1. Projděte si příručky a [kurzy](how-to-recognize-intents-from-speech-csharp.md)pro [rychlý Start](quickstart-csharp-dotnet-windows.md) ke službám Speech Services. Podívejte se také na [ukázky kódu](samples.md) , abyste získali zkušenosti s novými rozhraními API.
1. Aktualizujte aplikaci tak, aby používala hlasové služby.

## <a name="support"></a>Podpora

Zákazníci s Zpracování řeči Bingu by měli kontaktovat zákaznickou podporu otevřením [lístku podpory](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Pokud vaše podpora vyžaduje [plán technické podpory](https://azure.microsoft.com/support/plans/), můžete nás taky kontaktovat.

Podporu služby Speech Services, sady SDK a rozhraní API najdete na [stránce podpory](support.md)služby pro rozpoznávání řeči.

## <a name="next-steps"></a>Další postup

* [Vyzkoušejte si hlasové služby zdarma](get-started.md)
* [Rychlé zprovoznění: Rozpoznávání řeči v aplikaci pro UWP pomocí sady Speech SDK](quickstart-csharp-uwp.md)

## <a name="see-also"></a>Viz také:
* [Poznámky k verzi služby Speech Services](releasenotes.md)
* [Co je Speech Service](overview.md)
* [Dokumentace ke službám Speech a sadě Speech SDK](speech-sdk.md#get-the-sdk)
