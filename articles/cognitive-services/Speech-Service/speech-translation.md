---
title: Překlad řeči Speech službami Azure
titlesuffix: Azure Cognitive Services
description: Hlasové služby umožňují přidat začátku do konce, v reálném čase, vícejazykového překlad řeči do aplikací, nástroje a zařízení. Stejného rozhraní API můžete použít pro překlad řeči řeči a rozpoznávání řeči na text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 84d212129c5225fd0efebfca5640cfc3d32e8a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072399"
---
# <a name="what-is-speech-translation"></a>Co je pro překlad řeči?

Překlad řeči z hlasové služby Azure, umožňuje v reálném čase, vícejazykového řeči řeči a rozpoznávání řeči na text překlad zvukové streamy. Se sadou SDK pro řeč aplikace, nástroje a zařízení mít přístup k přepisů zdroje a překladu výstupy pro zadaný zvuk. Dočasné určené k transkripci a překladu výsledky jsou vráceny podle zjištění řeči a finále výsledky mohou být převedeny na řečového.

Překlad modulu společnosti Microsoft využívá k tomu dva různé přístupy: statistické strojový překlad (SMT) a strojovému překladu neuronových (sítí NMT). SMT využívá pokročilé statistická odhadnout nejlepší možné překlady daném kontextu pár slov. S NMT neuronové sítě umožňují zajistit přesnější, přirozeného to nezní překlady pomocí úplný kontext věty překládat slova.

Dnes společnost Microsoft používá NMT pro překlad pro Nejoblíbenější jazyky. Všechny [jazyky dostupné pro překlad řeči speech](language-support.md#speech-translation) NMT využívají. Překlad řeči na text pomocí SMT nebo NMT v závislosti na pár jazyka. Pokud cílový jazyk je podporována NMT, úplný překlad je s využitím NMT. Cílový jazyk není podporován NMT, překlad při hybridním NMT a SMT, pomocí angličtina jako "kontingenční tabulku" mezi dva jazyků.

## <a name="core-features"></a>Základní funkce

Zde jsou funkce k dispozici prostřednictvím rozhraní REST API a sadou SDK pro řeč:

| Případ použití | Sada SDK | REST |
|----------|-----|------|
| Překlad řeči na text s výsledky rozpoznávání. | Ano | Ne |
| Překlad řeči speech. | Ano | Ne |
| Dočasné rozpoznávání a překladu výsledky. | Ano | Ne |

## <a name="get-started-with-speech-translation"></a>Začínáme s překlad řeči

Nabízíme rychlí průvodci navržená tak, aby se spouštěním kódu za méně než 10 minut. Tato tabulka obsahuje seznam šablon rychlý start překlad řeči uspořádané podle jazyka.

| Rychlý start | Platforma | API – referenční informace |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-translate-speech-dotnetcore-windows.md) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-translate-speech-dotnetframework-windows.md) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-translate-speech-uwp.md) | Windows | [Procházet](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-translate-speech-cpp-windows.md) | Windows | [Procházet](https://aka.ms/csspeech/cppref)|
| [Java](quickstart-translate-speech-java-jre.md) | Windows, Linux, macOS | [Procházet](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Ukázka kódu

Ukázkový kód pro zpracování řeči SDK je k dispozici na Githubu. Tyto ukázky zahrnují běžné scénáře, jako jsou čtení zvuk ze souboru nebo datový proud, průběžné a jednorázová rozpoznávání/překlad prostředků a práci s vlastní modely.

* [Ukázky řeči na text a překladu (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>Průvodce migrací

> [!WARNING]
> Translator Speech budou vyřazeny z provozu 15. října 2019.

Pokud vaše aplikace, nástroje nebo produkty používáte Translator Speech, jsme vytvořili, kteří vám pomůžeme s migrací hlasové služby.

* [Migrace z Translator Speech API do hlasové služby](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>Referenční dokumenty

* [Speech SDK](speech-sdk-reference.md)
* [Rozpoznávání řeči zařízení SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Text-to-speech](rest-text-to-speech.md)
* [REST API: Přepis služby batch a přizpůsobení](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Další postup

* [Získejte klíč předplatného hlasových služeb zdarma](get-started.md)
* [Získání sady SDK pro řeč](speech-sdk.md)
