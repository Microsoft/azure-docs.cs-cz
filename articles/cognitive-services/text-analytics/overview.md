---
title: Přehled analýzy textu – Azure Cognitive Services | Dokumentace Microsoftu
description: Pro analýzu textu ve službě Azure Cognitive Services pro analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a propojování entit.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 8/30/2018
ms.author: ashmaka
ms.openlocfilehash: 71af2bcbf58279681bbea66d4f76e951a2efce59
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341482"
---
# <a name="what-is-text-analytics"></a>Co je rozhraní Text Analytics?

Služba Text Analytics poskytuje pokročilé zpracování pro nezpracovaný text nestrukturovaných přirozeného jazyka. Zahrnuje čtyři hlavní funkce: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a propojování entit.

## <a name="analyze-sentiment"></a>Analýza mínění

[Přečtěte si](how-tos/text-analytics-how-to-sentiment-analysis.md) co si vaši zákazníci myslí o vaší značce nebo téma díky analýze nezpracovaný text pro příčiny o pozitivní nebo negativní zabarvení. Toto rozhraní API vrátí skóre mínění mezi 0 a 1 pro každý dokument, přičemž 1 je největší kladné.<br />
Modely analýzy jsou které je předem vytrénované pomocí rozsáhlé tělo technologií text a přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](text-analytics-supported-languages.md), rozhraní API můžete analyzovat a stanovíte jeho skóre nezpracovaný text, který zadáte.

## <a name="extract-key-phrases"></a>Extrakce klíčových frází

Automaticky [extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md) rychle identifikovat hlavní body. Mějme například vstupní text "potravinovém byla chutný a bylo vynikající pracovníci", služba Text Analytics vrátí hlavní témata body: "potravin" a "vynikající zaměstnanci".

## <a name="detect-language"></a>Zjištění jazyka

Až 120 jazyků [detekovat](how-tos/text-analytics-how-to-language-detection.md) které vstupní text je napsaný v jazyce a sestavy jeden jazyk kódu pro každý dokument podání žádosti. Kód jazyka je spárovaná s skóre označuje sílu skóre.

## <a name="identify-linked-entities-preview"></a>Identifikujte propojených entit (Preview)

[Identifikujte](how-tos/text-analytics-how-to-entity-linking.md) dobře známé entity v textu a odkaz na další informace na webu. Propojování entit rozpozná a umožňuje rozlišit při termín, který se používá jako jeden samostatně odlišitelným entitám, příkazy a další slovní druhy.

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: odeslání dat pro analýzy a popisovač výstupy ve vašem kódu. Analyzátory jsou využité jako-je bez další konfigurace nebo přizpůsobení.

1. [Zaregistrujte](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) pro [přístupový klíč](how-tos/text-analytics-how-to-access-key.md). Klíč musí být předán s každým požadavkem.

2. [Vytvořit žádost o](how-tos/text-analytics-how-to-call-api.md#json-schema) ve formátu JSON, který obsahuje vaše data jako nezpracovaný nestrukturovaného textu.

3. Odeslat požadavek na koncový bod vytvoří během registrace, přidávání rozhraní API, kterou chcete volat: analýzu mínění, extrakce klíčových frází, rozpoznávání jazyka nebo identifikace entit.

4. Stream nebo ukládat místně odpověď. V závislosti na požadavku jsou výsledky skóre mínění, kolekce extrahované klíčové fráze nebo kód jazyka.

Výstup se vrátí jako jeden dokument JSON, s výsledky pro každý dokument text, který jste odeslali, na základě ID. Pak můžete analyzovat, vizualizace nebo kategorizace výsledky na užitečné přehledy.

Operace, které provádí služba Text Analytics jsou bezstavové. Data nejsou uložená ve vašem účtu.

<a name="data-limits"></a>

## <a name="specifications"></a>Specifikace

### <a name="supported-languages"></a>Podporované jazyky

Zobrazit [podporované jazyky v rozhraní Text Analytics](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Omezení dat

Všechny koncové body služby pro analýzu textu přijímat data nezpracovaný text. Současný limit je 5 000 znaků pro každý dokument; Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit do menších bloků. Pokud pořád budete potřebovat vyšší limit, [kontaktujte nás](https://azure.microsoft.com/overview/sales-number/) tak, aby si popíšeme vašim požadavkům.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 000 znaků pohledu `String.Length`. |
| Maximální velikost celou žádost | 1 MB |
| Maximální počet dokumentů v požadavku | 1 000 dokumentů |

Omezení četnosti je 100 volání za minutu. Mějte na paměti, že můžete odeslat velké množství dokumentů v jednom volání (až 1 000 dokumentů).

### <a name="unicode-encoding"></a>Kódování Unicode

Služba Text Analytics používá kódování Unicode pro textové vyjádření a výpočty počet znaků. Můžete zaslat žádost v kódování UTF-8 nebo UTF-16 s žádné měřitelné rozdíly v počtu znaků. Pokud používáte `String.Length` získat počet znaků, že používáte stejnou metodu používáme k měření velikosti dat.

## <a name="next-steps"></a>Další postup

Nejprve zkuste [interaktivní ukázka](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Můžete vložit textový vstup (maximálně 5 000 znaků) k rozpoznání jazyka (až 120), vypočítat skóre mínění, extrahovat klíčové fráze nebo identifikovat propojených entit. Bez zápisu je nezbytné.

Až budete připravení přímo volat služby pro analýzu textu:

+ [Zaregistrujte](how-tos/text-analytics-how-to-signup.md) pro přístupový klíč a projděte si kroky pro [volání rozhraní API](how-tos/text-analytics-how-to-call-api.md).

+ [Rychlý Start](quickstarts/csharp.md) se volá návod k rozhraní REST API napsané v jazyce C#. Zjistěte, jak posílat text, zvolte analýzu a zobrazit výsledky s minimem kódu.

+ [Referenční dokumentace rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) poskytuje technická dokumentace k rozhraním REST API. V dokumentaci k podporuje vložené volání, takže můžete volat z každé stránky dokumentace k rozhraní API.

+ [Externí & obsah vytvořený komunitou](text-analytics-resource-external-community.md) obsahuje seznam blogových příspěvků a videa s představením toho, jak používat analýzu textu s ostatními nástroji a technologiemi.

## <a name="see-also"></a>Další informace najdete v tématech

 [Cognitive Services dokumentaci stránky](https://docs.microsoft.com/azure/cognitive-services/)
