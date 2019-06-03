---
title: Co je API pro analýzu textu? – Možnosti –
titleSuffix: Azure Cognitive Services
description: Použití rozhraní Text Analytics API z Azure Cognitive Services pro analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání entit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/03/2019
ms.author: aahi
ms.openlocfilehash: 7d52585b51af09c430130141c3680b5630f7b95e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417289"
---
# <a name="what-is-text-analytics-api"></a>Co je API pro analýzu textu?

Rozhraní Text Analytics API je Cloudová služba, která poskytuje pokročilé zpracování nezpracovaný text přirozeného jazyka a zahrnuje čtyři hlavní funkce: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání entit.

Rozhraní API je součástí [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), kolekce machine learningu a algoritmů AI v cloudu pro vaše vývojové projekty.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analýza textu může znamenat různé věci, ale ve službě Cognitive Services Text Analytics API poskytuje čtyři druhy analýzy, jak je popsáno níže.

## <a name="sentiment-analysis"></a>Analýza mínění
Použití [analýzu subjektivního hodnocení](how-tos/text-analytics-how-to-sentiment-analysis.md) a zjistěte, co si vaši zákazníci myslí o vaší značce nebo téma díky analýze nezpracovaný text pro příčiny o pozitivní nebo negativní zabarvení. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení.<br /> Modely analýzy jsou předem vytrénované pomocí rozsáhlého textu a technologiemi přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](text-analytics-supported-languages.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace. Můžete použít [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c9) rozhraní API nebo [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází
Automaticky [extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md) rychle identifikovat hlavní body. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál). Můžete použít [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c6) zde rozhraní API nebo [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="language-detection"></a>Rozpoznávání jazyka
Je možné [rozpoznat jazyk, který vstupní text je napsaný v](how-tos/text-analytics-how-to-language-detection.md) a tvorba sestav jeden jazyk kódu pro každý dokument podání žádosti v nejrůznějších jazycích, proměnných typu variant, dialekty a některé jazykové verze nebo místní jazyky. Kód jazyka spárovaný se skóre označuje sílu skóre. Můžete použít [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/56f30ceeeda5650db055a3c7) rozhraní API nebo [.NET](https://docs.microsoft.com/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) SDK.

## <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit
[Identifikujte a kategorizaci entity](how-tos/text-analytics-how-to-entity-linking.md) text jako lidech, místech, organizace, datum a čas, množství, procenta, měny a další. Také rozpozná známé entity a propojí je s dalšími informacemi na webu. Můžete použít [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/operations/5ac4251d5b4ccd1554da7634) rozhraní API.

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

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Prostředí pro více programování rozhraní text Analytics úrovně

I v případě, že nemáte spoustu zkušeností při programování můžete začít používat rozhraní API pro analýzu textu ve vašich procesů. Pomocí těchto kurzů se dozvíte, jak můžete použít rozhraní API pro analýzu textu různými způsoby podle vaši úroveň zkušeností. 

* Minimální programování:
    * [Použití rozhraní API pro analýzu textu a MS Flowu k identifikaci mínění komentáře ve skupině Yammeru](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrace Power BI pomocí rozhraní Text Analytics API pro analýzu zpětné vazby od zákazníků](tutorials/tutorial-power-bi-key-phrases.md)
* Programování v prostředí vhodné:
    * [Analýza mínění na streamovaných datech pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Sestavení aplikace Flask překlad textu, analýza mínění a syntetizace řeči](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

Tato část byla přesunuta do samostatného článku pro lepší vyhledatelnost. Informace o tomto obsahu naleznete v části [Podporované jazyky v rozhraní API pro analýzu textu](text-analytics-supported-languages.md).

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Všechny koncové body rozhraní API pro analýzu textu přijímají data v podobě nezpracovaného textu. Současný limit je 5 120 znaků pro každý dokument; Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit do menších bloků. Pokud budete přesto potřebovat vyšší limit, [kontaktujte nás](https://azure.microsoft.com/overview/sales-number/), abychom mohli projednat vaše požadavky.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 120 znaků, měřený podle [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximální velikost celého požadavku | 1 MB |
| Maximální počet dokumentů v požadavku | 1 000 dokumentů |

Omezení četnosti je 100 požadavků na druhý a 1 000 požadavků za minutu. Můžete odeslat velké množství dokumentů v jednom volání (až 1 000 dokumentů).

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API pro analýzu textu používá kódování Unicode pro textové vyjádření a kalkulaci počtu znaků. Požadavky je možné odesílat v kódování UTF-8 i UTF-16 bez měřitelných rozdílů v počtu znaků. Body kódu sady Unicode jsou použité jako heuristika délky znaků a jsou považovány za ekvivalentní ve smyslu datového omezení analýzy textu. Pokud používáte [ `StringInfo.LengthInTextElements` ](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) získat počet znaků, použijete stejnou metodu používáme k měření velikosti dat.

## <a name="next-steps"></a>Další postup

+ [Zaregistrujte se](how-tos/text-analytics-how-to-signup.md) pro přístupový klíč a projděte si kroky pro [volání rozhraní API](how-tos/text-analytics-how-to-call-api.md).

+ [Rychlý start](quickstarts/csharp.md) obsahuje vysvětlení volání rozhraní REST API napsaných v jazyce C#. Zjistěte, jak odesílat text, zvolit analýzu a zobrazit výsledky s minimem kódu. Pokud dáváte přednost, můžete začít s [rychlý start Python](quickstarts/python.md) místo.

+ Podrobněji trochu blíže se seznámíte s tím [kurz analýzy subjektivního hodnocení](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) pomocí Azure Databricks.

+ Projděte si náš seznam blogových příspěvků a další videa o tom, jak použít rozhraní Text Analytics API s ostatními nástroji a technologiemi v našich [externí & obsah vytvořený komunitou stránky](text-analytics-resource-external-community.md).
