---
title: Přehled rozhraní API Analytics text - kognitivní služeb Azure | Microsoft Docs
description: Rozhraní API pro analýzy text v kognitivní služby Azure pro analýzu postojích, extrakce klíče frázi a zjišťování jazyka.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343634"
---
# <a name="what-is-text-analytics-api-version-20"></a>Co je Text Analytics rozhraní API verze 2.0?

Rozhraní API Analytics textu je Cloudová služba, která poskytuje pokročilé přirozeného jazyka zpracování přes nezpracovaný text a zahrnuje čtyři hlavní funkce: postojích analýzy, klíče frázi extrakce, zjišťování jazyka a propojování entity.

Rozhraní API je zálohovaný díky prostředky v [kognitivní služby Microsoft](https://docs.microsoft.com/azure/cognitive-services/), kolekce strojového učení a algoritmy AI v cloudu, snadno použití ve vašich projektů vývoj.

## <a name="capabilities-in-text-analytics"></a>Možnosti v Analýza textu

Analýza textu může zahrnovat různých věcí, ale v kognitivní služby, rozhraní API Text Analytics poskytuje čtyři typy analýzy, jak je popsáno v následující tabulce.

| Operace| Popis | Rozhraní API |
|-----------|-------------|------|
|[**Analýza postojích**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Zjistěte, co zákazníci myslíte brand nebo téma analýzou nezpracovaný text pro, který následuje postojích kladné a záporné. Toto rozhraní API vrátí postojích skóre mezi 0 a 1 pro každý dokument, kde je většina kladné 1.<br /> Analýza modely jsou pretrained pomocí rozsáhlé část text a přirozeného jazyka technologie společnosti Microsoft. Pro [vybrané jazyky](text-analytics-supported-languages.md), analyzovat a stanovení skóre všechny nezpracované text, který zadáte, přímo vrací výsledky do aplikace volání rozhraní API. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Rozbalení klíče fráze**](how-tos/text-analytics-how-to-keyword-extraction.md) | Automaticky extrahujte klíče frází rychle identifikovat hlavní body. Například pro vstupní text "jídlo byla chutný a nebyly k dispozici vynikající pracovníci", rozhraní API vrátí hlavní body čtených: "jídlo" a "vynikající zaměstnanci".  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Zjišťování jazyka**](how-tos/text-analytics-how-to-language-detection.md) | Až 120 jazyků zjistit jazyk, který vstupní text je napsaný ve a ohlásit jeden jazyk kódu pro každý dokument odeslaných v požadavku. Kód jazyka je spárován s skóre, která určuje, jaká síla skóre. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Entity propojení (Preview)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifikujte dobře známé entity v textu a odkaz na další informace na webu. Propojování entity rozpozná a umožňuje rozlišit při termín, který se používá jako jedna ze samostatně odlišit entity, příkazy a jiných forem aplikace word. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: Odeslat data pro analýzu a popisovač výstupy ve vašem kódu. Analyzátory spotřebování jako-je, bez další konfigurace a přizpůsobení.

1. [Zaregistrujte si](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro [přístupový klíč](how-tos/text-analytics-how-to-access-key.md). Klíč musí být předán na každý požadavek.

2. [Žádost o formulovali](how-tos/text-analytics-how-to-call-api.md#json-schema) obsahující data jako nezpracovaná nestrukturovaných text ve formátu JSON.

3. Odeslat požadavek na koncový bod vytvoří během registrace, připojování požadovaným prostředkem: Analýza postojích klíče frázi extrakce, zjišťování jazyka nebo identifikace entity.

4. Datový proud nebo úložiště odpovědi lokálně. V závislosti na žádost jsou výsledky postojích skóre, kolekci extrahované klíče fráze nebo kód jazyka.

Výstup se vrátí jako jeden dokument JSON, s výsledky pro každý dokument text, který jste odeslali, na základě ID. Následně můžete analyzovat, vizualizovat nebo zařadit výsledky do prakticky využitelné informace.

Data nejsou uložená v účtu. Operace provedené rozhraní API Analytics Text jsou bezstavové, což znamená zpracována text, který zadáte a výsledky jsou vráceny okamžitě.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

V této části se přesunul do věnovaný samostatný článek pro zajistí lepší vyhledatelnost. Odkazovat na [podporované jazyky v textu Analytics API](text-analytics-supported-languages.md) pro tento obsah.

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Koncové body Text Analytics API přijímat data nezpracovaný text. Současný limit je 5 000 znaků pro každý dokument; Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit na menší bloky. Pokud stále potřebujete vyšší limit, [kontaktujte nás](https://azure.microsoft.com/overview/sales-number/) tak, aby probereme vaše požadavky.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 000 znaků měřený podle `String.Length`. |
| Maximální velikost celého požadavku | 1 MB |
| Maximální počet dokumentů v požadavku | 1000 dokumentů |

Limit rychlosti je 100 volání za minutu. Všimněte si, že můžete odeslat velké množství dokumentů v jediném volání (maximálně 1000 dokumentů).

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API Analytics Text používá kódování Unicode pro textovou reprezentaci a výpočty počet znaků. Požadavky můžete odeslána ve formátu UTF-8 a UTF-16 s žádné měřitelné rozdíly v počtu znaků. Unicode body jsou použity jako heuristiky pro znaků a jsou považovány za ekvivalentní pro účely text analýzy dat omezení. Pokud používáte `String.Length` získat počet znaků, používáte stejnou metodu používáme k měření velikost dat.

## <a name="next-steps"></a>Další postup

Nejdřív, zkuste [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Můžete vložit zadávání textu (maximálně 5 000 znaků) Chcete-li zjistit jazyk (až 120), výpočtu skóre postojích nebo extrahování klíčových frází. Bez registrace potřeby.

Až budete připravení přímo volat rozhraní API:

+ [Zaregistrujte si](how-tos/text-analytics-how-to-signup.md) pro přístup klíče a projděte si kroky pro [volání rozhraní API](how-tos/text-analytics-how-to-call-api.md).

+ [Rychlý Start](quickstarts/csharp.md) je návod, REST API volá napsané v jazyce C#. Zjistěte, jak k odeslání text, zvolte analýzu a zobrazení výsledků s minimálním kódem.

+ [Referenční dokumentace rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) poskytuje technická dokumentace k rozhraní API. V dokumentaci podporuje embedded volání, takže můžete volat rozhraní API z každé stránce dokumentace.

+ [Externí & komunity obsahu](text-analytics-resource-external-community.md) obsahuje seznam příspěvcích na blogu a videa, který ukazuje, jak používat Analýza textu pomocí jiných nástrojů a technologií.

## <a name="see-also"></a>Další informace najdete v tématech

 [Kognitivní stránky dokumentace služby](https://docs.microsoft.com/azure/cognitive-services/)
