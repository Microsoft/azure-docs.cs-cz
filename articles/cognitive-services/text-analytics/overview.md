---
title: Co je Analýza textu?
titleSuffix: Azure Cognitive Services
description: Analýza textu ve službě Azure Cognitive Services k analýze mínění, extrakci klíčových frází, rozpoznávání jazyka a spojování entit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 02/13/2019
ms.author: aahi
ms.openlocfilehash: 0de4e0d750d8ae3061ed0b80d706dec545338a90
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242921"
---
# <a name="what-is-text-analytics"></a>Co je Analýza textu?

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje rozšířené zpracování přirozeného jazyka pro nezpracovaný text a zahrnuje čtyři hlavní funkce: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a spojování entit.

Rozhraní API je součástí [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), kolekce machine learningu a algoritmů AI v cloudu pro vaše vývojové projekty.

## <a name="capabilities-in-text-analytics"></a>Možnosti služby Analýza textu

Analýza textu může znamenat různé věci, ale ve službě Cognitive Services poskytuje rozhraní API pro analýzu textu čtyři druhy analýzy, jak je popsáno v následující tabulce.

| Operace| Popis | Rozhraní API |
|-----------|-------------|------|
|[**Analýza mínění**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Zjistěte, co si vaši zákazníci myslí o vaší značce nebo tématu díky analýze nezpracovaného textu k odhalení signálů pozitivního nebo negativního mínění. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení.<br /> Modely analýzy jsou předem vytrénované pomocí rozsáhlého textu a technologiemi přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](text-analytics-supported-languages.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Extrakce klíčových frází**](how-tos/text-analytics-how-to-keyword-extraction.md) | Automatická extrakce klíčových frází a rychlé zjištění hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Rozpoznávání jazyka**](how-tos/text-analytics-how-to-language-detection.md) | Služba rozpozná, v jakém jazyce je vstupní text napsaný a každému odeslanému dokumentu na žádost přiřadí kód jazyka až pro 120 jazyků. Kód jazyka spárovaný se skóre označuje sílu skóre. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Rozpoznávání entit (Preview)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifikuje a zařadí entity v textu do kategorií jako jsou lidé, místa, organizace, datum a čas, množství, procenta, měny a další. Také rozpozná známé entity a propojí je s dalšími informacemi na webu. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634) |

## <a name="use-containers"></a>Použití kontejnerů

[Kontejnery pro analýzu textu](how-tos/text-analytics-how-to-install-containers.md) extrahovat klíčové fráze, detekce jazyka a analýza sentimentu místně, nainstalováním standardizované kontejnery Dockeru blíž ke svým datům.

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: odešlete data pro analýzu a obdržíte výstupy ve svém kódu. Analyzátory jsou používány tak, jak jsou, bez dalších konfigurací nebo přizpůsobení.

1. [Zaregistrujte se](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a získejte [přístupový klíč](how-tos/text-analytics-how-to-access-key.md). Klíč musí být předán s každým požadavkem.

2. [Zformulujte požadavek](how-tos/text-analytics-how-to-call-api.md#json-schema) obsahující vaše data ve formě nezpracovaného nestrukturovaného textu ve formátu JSON.

3. Odešlete požadavek na koncový bod vytvořený během registrace a připojte požadovaný prostředek: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka nebo identifikaci entit.

4. Streamujte nebo ukládejte odpověď místně. V závislosti na požadavku jsou výsledky ve formě skóre mínění, kolekce extrahovaných klíčových frází nebo kódu jazyka.

Výstup se vrátí jako jeden dokument JSON s výsledky pro každý textový dokument, který jste odeslali, na základě ID. Následně můžete analyzovat, vizualizovat nebo kategorizovat výsledky na užitečné přehledy.

Data se neukládají ve vašem účtu. Operace prováděné prostřednictvím rozhraní API pro analýzu textu jsou bezstavové, což znamená poskytnutý text se zpracuje a výsledky vrátí okamžitě.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

Tato část byla přesunuta do samostatného článku pro lepší vyhledatelnost. Informace o tomto obsahu naleznete v části [Podporované jazyky v rozhraní API pro analýzu textu](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Všechny koncové body rozhraní API pro analýzu textu přijímají data v podobě nezpracovaného textu. Současný limit je 5 000 znaků pro každý dokument. Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit do menších bloků. Pokud budete přesto potřebovat vyšší limit, [kontaktujte nás](https://azure.microsoft.com/overview/sales-number/), abychom mohli projednat vaše požadavky.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 000 znaků pohledu [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximální velikost celého požadavku | 1 MB |
| Maximální počet dokumentů v požadavku | 1 000 dokumentů |

Omezení četnosti je 100 volání za minutu. Upozorňujeme, že můžete odeslat velké množství dokumentů v jednom volání (až 1 000 dokumentů).

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API pro analýzu textu používá kódování Unicode pro textové vyjádření a kalkulaci počtu znaků. Požadavky je možné odesílat v kódování UTF-8 i UTF-16 bez měřitelných rozdílů v počtu znaků. Body kódu sady Unicode jsou použité jako heuristika délky znaků a jsou považovány za ekvivalentní ve smyslu datového omezení analýzy textu. Pokud používáte [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) získat počet znaků, použijete stejnou metodu používáme k měření velikosti dat.

## <a name="next-steps"></a>Další postup

Nejprve zkuste [interaktivní ukázku](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Můžete vložit textový vstup (maximálně 5 000 znaků) a zjistit jazyk (až 120), vypočítat skóre mínění nebo extrahovat klíčové fráze. Registrace není potřeba.

Jakmile budete připraveni volat rozhraní API přímo:

+ [Zaregistrujte se](how-tos/text-analytics-how-to-signup.md) pro přístupový klíč a projděte si kroky pro [volání rozhraní API](how-tos/text-analytics-how-to-call-api.md).

+ [Rychlý start](quickstarts/csharp.md) obsahuje vysvětlení volání rozhraní REST API napsaných v jazyce C#. Zjistěte, jak odesílat text, zvolit analýzu a zobrazit výsledky s minimem kódu.

+ [Referenční dokumentace k rozhraní API](//go.microsoft.com/fwlink/?LinkID=759346) obsahuje technickou dokumentaci k rozhraním API. Dokumentace podporuje vložená volání, takže můžete volat rozhraní API z každé stránky dokumentace.

+ [Externí obsah a obsah vytvořený komunitou](text-analytics-resource-external-community.md) obsahuje seznam blogových příspěvků a videí, které ukazují, jak používat službu Analýza textu s dalšími nástroji a technologiemi.

## <a name="see-also"></a>Další informace najdete v tématech

 [Stránka dokumentace ke službám Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)
