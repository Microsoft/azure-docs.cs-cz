---
title: Co je rozhraní API pro analýzu textu? Vestavěn
titleSuffix: Azure Cognitive Services
description: Použijte rozhraní API pro analýzu textu z Azure Cognitive Services pro analýzu mínění, extrakci klíčových frází, detekci jazyka a rozpoznávání entit.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/07/2019
ms.author: aahi
ms.openlocfilehash: 0f16c687f9816e99e05c2495a05596bd988ca9d2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827318"
---
# <a name="what-is-the-text-analytics-api"></a>Co je rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje pokročilé zpracování přirozeného jazyka nad nezpracovaným textem a zahrnuje čtyři hlavní funkce: analýzu míněníí, extrakci klíčových frází, rozpoznávání jazyka a rozpoznávání entit.

Rozhraní API je součástí služby [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/), kolekce algoritmů strojového učení a AI v cloudu pro vývojové projekty.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Understanding-Text-using-Cognitive-Services/player]

Analýza textu může znamenat různé věci, ale v Cognitive Services rozhraní API pro analýzu textu poskytuje čtyři typy analýz, jak je popsáno níže. Tyto funkce můžete použít spolu s [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)nebo s [klientskou knihovnou](quickstarts/text-analytics-sdk.md).

## <a name="sentiment-analysis"></a>Analýza mínění
Pomocí [analýzy mínění](how-tos/text-analytics-how-to-sentiment-analysis.md) zjistíte, co si zákazníci myslí o vaší značce nebo tématu analýzou nezpracovaného textu pro poznámky o kladných nebo záporných mínění. Toto rozhraní API vrátí pro každý dokument skóre mínění mezi 0 a 1, přičemž 1 je mez pro nejvíce kladné hodnocení.<br /> Modely analýzy jsou předem vytrénované pomocí rozsáhlého textu a technologiemi přirozeného jazyka od Microsoftu. Pro [vybrané jazyky](text-analytics-supported-languages.md) může rozhraní API analyzovat a stanovit skóre jakéhokoliv nezpracovaného textu, který zadáte, přičemž vrátí výsledky přímo do volající aplikace.

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází
Automatické [extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md) pro rychlé určení hlavních bodů. Například pro vstupní text „The food was delicious and there were wonderful staff“ (Jídlo bylo výborné a personál byl úžasný),vrací rozhraní API hlavní body: „food“ (jídlo) a „wonderful staff“ (úžasný personál).

## <a name="language-detection"></a>Rozpoznávání jazyka
Můžete [zjistit, který jazyk je vstupní text napsán](how-tos/text-analytics-how-to-language-detection.md) , a nahlásit jeden kód jazyka pro každý dokument odeslaný v žádosti v široké škále jazyků, variant, dialektů a některých regionálních/kulturních jazyků. Kód jazyka spárovaný se skóre označuje sílu skóre.

## <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit
[Identifikujte a Kategorizujte entity](how-tos/text-analytics-how-to-entity-linking.md) v textu jako lidi, místa, organizace, datum a čas, množství, procenta, měny a další. Také rozpozná známé entity a propojí je s dalšími informacemi na webu.

## <a name="use-containers"></a>Použití kontejnerů

[Kontejnery analýza textu můžete použít](how-tos/text-analytics-how-to-install-containers.md) k extrakci klíčových frází, zjišťování jazyka a k analýze mínění místně, a to díky instalaci standardizovaných kontejnerů Docker blíž k vašim datům.

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: odešlete data pro analýzu a obdržíte výstupy ve svém kódu. Analyzátory jsou používány tak, jak jsou, bez dalších konfigurací nebo přizpůsobení.

1. [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro analýza textu. Potom [získáte klíč](../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) , který jste vygenerovali k ověření vašich požadavků.

2. [Zformulujte požadavek](how-tos/text-analytics-how-to-call-api.md#json-schema) obsahující vaše data ve formě nezpracovaného nestrukturovaného textu ve formátu JSON.

3. Odešlete požadavek na koncový bod vytvořený během registrace a připojte požadovaný prostředek: analýzu mínění, extrakci klíčových frází, rozpoznávání jazyka nebo identifikaci entit.

4. Streamujte nebo ukládejte odpověď místně. V závislosti na požadavku jsou výsledky ve formě skóre mínění, kolekce extrahovaných klíčových frází nebo kódu jazyka.

Výstup se vrátí jako jeden dokument JSON s výsledky pro každý textový dokument, který jste odeslali, na základě ID. Následně můžete analyzovat, vizualizovat nebo kategorizovat výsledky na užitečné přehledy.

Data se neukládají ve vašem účtu. Operace prováděné prostřednictvím rozhraní API pro analýzu textu jsou bezstavové, což znamená poskytnutý text se zpracuje a výsledky vrátí okamžitě.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analýza textu pro více úrovní prostředí pro programování

Můžete začít používat rozhraní API pro analýzu textu v procesech, a to i v případě, že při programování nemáte hodně zkušeností. Pomocí těchto kurzů se dozvíte, jak můžete použít rozhraní API k analýze textu různými způsoby, aby odpovídaly vaší úrovni prostředí. 

* Vyžaduje se minimální programování:
    * [Použití rozhraní API pro analýzu textu a MS Flow k identifikaci mínění komentářů ve skupině Yammeru](https://docs.microsoft.com/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Integrace Power BI s rozhraní API pro analýzu textu k analýze názoru zákazníků](tutorials/tutorial-power-bi-key-phrases.md)
* Doporučené programovací prostředí:
    * [Analýza mínění dat streamování pomocí Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)
    * [Sestavení aplikace v baňce pro překlad textu, analýza mínění a syntetizace řeči](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2F%2Fazure%2Fcognitive-services%2Ftext-analytics%2Ftoc.json&bc=%2F%2Fazure%2Fbread%2Ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

Tato část byla přesunuta do samostatného článku pro lepší vyhledatelnost. V rozhraní API pro analýzu textu pro tento obsah se můžete podívat na [podporované jazyky](text-analytics-supported-languages.md) .

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Všechny koncové body rozhraní API pro analýzu textu přijímají data v podobě nezpracovaného textu. Aktuální limit je 5 120 znaků pro každý dokument; Pokud potřebujete analyzovat větší dokumenty, můžete je rozdělit do menších bloků.

| Omezení | Hodnota |
|------------------------|---------------|
| Maximální velikost jednoho dokumentu | 5 120 znaků měřených [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements). |
| Maximální velikost celého požadavku | 1 MB |
| Maximální počet dokumentů v požadavku | 1 000 dokumentů |

Vaše omezení četnosti se bude lišit od cenové úrovně.

| Vrstva          | Počet požadavků za sekundu | Žádosti za minutu |
|---------------|---------------------|---------------------|
| S/více službami | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

Požadavky se měří pro každou funkci Analýza textu samostatně. Můžete například odeslat maximální počet požadavků pro vaši cenovou úroveň všem funkcím ve stejnou dobu.      

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API pro analýzu textu používá kódování Unicode pro textové vyjádření a kalkulaci počtu znaků. Požadavky je možné odesílat v kódování UTF-8 i UTF-16 bez měřitelných rozdílů v počtu znaků. Body kódu sady Unicode jsou použité jako heuristika délky znaků a jsou považovány za ekvivalentní ve smyslu datového omezení analýzy textu. Pokud k získání počtu znaků použijete [`StringInfo.LengthInTextElements`](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) , používáte stejnou metodu, kterou používáme k měření velikosti dat.

## <a name="next-steps"></a>Další kroky

+ [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro analýza textu, abyste získali klíč a koncový bod pro vaše aplikace.

+ Pomocí [rychlého](quickstarts/text-analytics-sdk.md) startu spustíte odesílání volání rozhraní API. Zjistěte, jak odesílat text, zvolit analýzu a zobrazit výsledky s minimem kódu.

+ Další informace o nových verzích a funkcích najdete [v tématu Co je nového v rozhraní API pro analýzu textu](whats-new.md) .

+ Dig se tento [kurz analýzy mínění](https://docs.microsoft.com/azure/azure-databricks/databricks-sentiment-analysis-cognitive-services) pomocí Azure Databricks trochu hlubší.

+ Podívejte se na náš seznam blogových příspěvků a další videa o použití rozhraní API pro analýzu textu s dalšími nástroji a technologiemi na naší [externí stránce obsahu komunity &](text-analytics-resource-external-community.md).
