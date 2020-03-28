---
title: Co je rozhraní API pro analýzu textu? - Schopnosti -
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní API pro analýzu textových analýz z Azure Cognitive Services pro analýzu mínění, extrakci klíčových frází, detekci jazyka a rozpoznávání entit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: a9519be591581fa434825f1a1fb31749788a21a8
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78395718"
---
# <a name="what-is-the-text-analytics-api"></a>Co je rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje pokročilé zpracování přirozeného jazyka přes nezpracovaný text a zahrnuje čtyři hlavní funkce: analýzu mínění, extrakci klíčových frází, detekci jazyka a rozpoznávání pojmenovaných entit.

Rozhraní API je součástí [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), kolekce algoritmů strojového učení a AI v cloudu pro vaše vývojové projekty.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analýza textu může znamenat různé věci, ale v rozhraní Cognitive Services rozhraní API pro analýzu textu poskytuje čtyři typy analýz, jak je popsáno níže. Tyto funkce můžete použít s [rozhraním REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)nebo [klientskou knihovnou](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Analýza mínění
Pomocí [analýzy mínění](how-tos/text-analytics-how-to-sentiment-analysis.md) zjistíte, co si zákazníci myslí o vaší značce nebo tématu, a to analýzou nezpracovaného textu, abyste zjistili, jaké jsou informace o pozitivním nebo negativním sentimentu. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení.<br /> Modely analýzy jsou předem vytrénované pomocí rozsáhlého textu a technologiemi přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](text-analytics-supported-languages.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace.

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází
Automaticky [extrahujte klíčové fráze,](how-tos/text-analytics-how-to-keyword-extraction.md) abyste rychle identifikovali hlavní body. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

## <a name="language-detection"></a>Rozpoznávání jazyka
Můžete [zjistit, ve kterém jazyce je vstupní text napsán,](how-tos/text-analytics-how-to-language-detection.md) a nahlásit jeden kód jazyka pro každý dokument odeslaný na žádost v široké škále jazyků, variant, dialektů a některých regionálních/kulturních jazyků. Kód jazyka spárovaný se skóre označuje sílu skóre.

## <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit
[Identifikujte a kategorizujte entity](how-tos/text-analytics-how-to-entity-linking.md) v textu jako osoby, místa, organizace, datum a čas, množství, procenta, měny a další. Také rozpozná známé entity a propojí je s dalšími informacemi na webu.

## <a name="use-containers"></a>Použití kontejnerů

[Pomocí kontejnerů Analýzy textu](how-tos/text-analytics-how-to-install-containers.md) můžete extrahovat klíčové fráze, detekovat jazyk a analyzovat mínění místně, a to instalací standardizovaných kontejnerů Dockeru blíže k vašim datům.

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: odešlete data pro analýzu a obdržíte výstupy ve svém kódu. Analyzátory jsou používány tak, jak jsou, bez dalších konfigurací nebo přizpůsobení.

1. [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro analýzu textu. Poté [získejte klíč](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) generovaný pro ověření vašich požadavků.

2. [Zformulujte požadavek](how-tos/text-analytics-how-to-call-api.md#json-schema) obsahující vaše data ve formě nezpracovaného nestrukturovaného textu ve formátu JSON.

3. Zaúčtuje požadavek do koncového bodu vytvořeného během registrace a připojí požadovaný prostředek: analýzu mínění, extrakci klíčových frází, detekci jazyka nebo rozpoznávání pojmenovaných entit.

4. Streamujte nebo ukládejte odpověď místně. V závislosti na požadavku jsou výsledky ve formě skóre mínění, kolekce extrahovaných klíčových frází nebo kódu jazyka.

Výstup se vrátí jako jeden dokument JSON s výsledky pro každý textový dokument, který jste odeslali, na základě ID. Následně můžete analyzovat, vizualizovat nebo kategorizovat výsledky na užitečné přehledy.

Data se neukládají ve vašem účtu. Operace prováděné prostřednictvím rozhraní API pro analýzu textu jsou bezstavové, což znamená poskytnutý text se zpracuje a výsledky vrátí okamžitě.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analýza textu pro více úrovní zkušeností s programováním

Rozhraní API pro analýzu textu můžete začít používat ve svých procesech, i když nemáte moc zkušeností s programováním. Pomocí těchto kurzů se dozvíte, jak můžete pomocí rozhraní API analyzovat text různými způsoby, aby odpovídaly vaší úrovni prostředí. 

* Minimální programování potřebné:
    * [Extrahovat informace v Excelu pomocí analýzy textu a Power Automate](tutorials/extract-excel-information.md)
    * [Použití rozhraní API pro analýzu textu a Toku MS k identifikaci mínění komentářů ve skupině Yammeru](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrace Power BI s rozhraním API pro analýzu textových analýz a analýza zpětné vazby od zákazníků](tutorials/tutorial-power-bi-key-phrases.md)
* Doporučené zkušenosti s programováním:
    * [Analýza mínění na streamovaných datech s využitím Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Vytvoření aplikace Flask pro překlad textu, analýzu mínění a syntézu řeči](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

Tato část byla přesunuta do samostatného článku pro lepší vyhledatelnost. Tento obsah naleznete [v podporovaných jazycích v rozhraní API pro analýzu textu.](text-analytics-supported-languages.md)

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Všechny koncové body rozhraní API pro analýzu textu přijímají data v podobě nezpracovaného textu. Aktuální limit je 5 120 znaků pro každý dokument; Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit na menší bloky.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 120 znaků měřeno [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximální velikost celého požadavku | 1 MB |
| Maximální počet dokumentů v požadavku | 1 000 dokumentů |

Limit sazby se bude lišit v závislosti na cenové úrovni.

| Úroveň          | Počet žádostí za sekundu | Požadavky za minutu |
|---------------|---------------------|---------------------|
| S / Vícenásobné služby | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Požadavky se měří pro každou funkci Analýzy textu zvlášť. Můžete například odeslat maximální počet požadavků na cenovou úroveň každé funkci současně.      

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API pro analýzu textu používá kódování Unicode pro textové vyjádření a kalkulaci počtu znaků. Požadavky je možné odesílat v kódování UTF-8 i UTF-16 bez měřitelných rozdílů v počtu znaků. Body kódu sady Unicode jsou použité jako heuristika délky znaků a jsou považovány za ekvivalentní ve smyslu datového omezení analýzy textu. Pokud používáte [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) k získání počtu znaků, používáte stejnou metodu, kterou používáme k měření velikosti dat.

## <a name="next-steps"></a>Další kroky

+ [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro analýzu textu, abyste získali klíč a koncový bod pro vaše aplikace.

+ Pomocí [rychlého startu](quickstarts/text-analytics-sdk.md) můžete začít odesílat volání rozhraní API. Zjistěte, jak odesílat text, zvolit analýzu a zobrazit výsledky s minimem kódu.

+ Informace o nových verzích a funkcích najdete [v rozhraní API pro analýzu textu.](whats-new.md)

+ S tímto [kurzem analýzy mínění](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) pomocí Azure Databricks se můžete trochu hlouběji zabývat.

+ Podívejte se na náš seznam blogových příspěvků a další videa o tom, jak používat rozhraní API pro analýzu textu s dalšími nástroji a technologiemi na naší [stránce Externí & komunitní obsah](text-analytics-resource-external-community.md).
