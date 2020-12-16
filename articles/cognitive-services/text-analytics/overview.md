---
title: Dolování a analýza textu pomocí rozhraní API pro analýzu textu – Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: Přečtěte si o dolování textu pomocí rozhraní API pro analýzu textu. Slouží k mínění analýze, rozpoznávání jazyka a dalším formám zpracování přirozeného jazyka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/17/2020
ms.author: aahi
keywords: dolování textu, analýza míněníí, analýza textu
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 6cef9dc65a72134e0aa70db5f89f4934263c48b4
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563224"
---
# <a name="what-is-the-text-analytics-api"></a>Co je rozhraní API pro analýzu textu?

Rozhraní API pro analýzu textu je cloudová služba, která poskytuje funkce NLP (přirozený jazyk Processing) pro dolování textu a analýzu textu, včetně analýz mínění, dolování stanovisek, extrakce klíčových frází, rozpoznávání jazyka a rozpoznávání pojmenovaných entit.

Rozhraní API je součástí služby [Azure Cognitive Services](../index.yml), kolekce algoritmů strojového učení a AI v cloudu pro vývojové projekty. Tyto funkce můžete použít spolu s [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1/)nebo s [klientskou knihovnou](quickstarts/client-libraries-rest-api.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

## <a name="sentiment-analysis"></a>Analýza mínění

Využijte [analýzu mínění](how-tos/text-analytics-how-to-sentiment-analysis.md) a zjistěte, co lidé považují za vaši značku nebo téma, a to tak, že budou v textu popsány kladné nebo záporné mínění. 

Tato funkce poskytuje popisky mínění (například "negativní", "neutrální" a "pozitivní") na základě nejvyššího skóre spolehlivosti zjištěného službou ve větě a na úrovni dokumentu. Tato funkce také vrátí skóre spolehlivosti mezi 0 a 1 pro každý dokument & větě v rámci něj pro pozitivní, neutrální a negativní mínění. Službu můžete také spustit místně [pomocí kontejneru](how-tos/text-analytics-how-to-install-containers.md).

Počínaje verzí v 3.1 verze Preview je dolování stanovisek funkcí Analýza mínění. Tato funkce také označovaná jako Analýza mínění založená na aspektech při zpracování přirozeného jazyka (NLP). Tato funkce poskytuje podrobnější informace o stanoviscích souvisejících s aspekty (jako jsou atributy produktů nebo služeb) v textu.

## <a name="key-phrase-extraction"></a>Extrakce klíčových frází

Pomocí [extrakce klíčových frází](how-tos/text-analytics-how-to-keyword-extraction.md) můžete rychle identifikovat hlavní koncepty v textu. Například v textu "potravina byla Delicious a dostali jsme milované pracovníky", Extrakce klíčových frází vrátí hlavní body pro komunikaci: "jídla" a "milované pracovníky".

## <a name="language-detection"></a>Rozpoznávání jazyka

Rozpoznání jazyka může [detekovat jazyk, ve kterém je vstupní text napsán](how-tos/text-analytics-how-to-language-detection.md) , a nahlásit jeden kód jazyka pro každý dokument odeslaný v žádosti v široké škále jazyků, variant, dialektů a některých regionálních/kulturních jazyků. Kód jazyka se spáruje s skóre spolehlivosti.

## <a name="named-entity-recognition"></a>Rozpoznávání pojmenovaných entit

Rozpoznávání pojmenovaných entit (NER) dokáže [identifikovat a kategorizovat entity](how-tos/text-analytics-how-to-entity-linking.md) ve vašem textu, protože uživatelé, místa, organizace, množství, známé entity jsou také rozpoznány a propojeny s dalšími informacemi na webu.

## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

[Použijte kontejnery analýza textu](how-tos/text-analytics-how-to-install-containers.md) k nasazení funkcí rozhraní API místně. Tyto kontejnery Docker umožňují přiblížit službu k vašim datům pro účely dodržování předpisů, zabezpečení nebo jiných provozních důvodů. Analýza textu nabízí následující kontejnery:

* Analýza mínění
* extrakce klíčových frází (Preview)
* rozpoznávání jazyka (Preview)
* Analýza textu pro stav (Preview)

## <a name="asynchronous-operations"></a>Asynchronních operace

`/analyze`Koncový bod umožňuje [asynchronní](how-tos/text-analytics-how-to-call-api.md)použití vybraných funkcí rozhraní API pro analýzu textu, jako je například ner a extrakce klíčových frází.

## <a name="typical-workflow"></a>Typický pracovní postup

Pracovní postup je jednoduchý: odešlete data pro analýzu a obdržíte výstupy ve svém kódu. Analyzátory jsou používány tak, jak jsou, bez dalších konfigurací nebo přizpůsobení.

1. [Vytvořte prostředek Azure](how-tos/text-analytics-how-to-call-api.md) pro analýza textu. Potom [získáte klíč](how-tos/text-analytics-how-to-call-api.md) , který jste vygenerovali k ověření vašich požadavků.

2. [Zformulujte požadavek](how-tos/text-analytics-how-to-call-api.md#json-schema) obsahující vaše data ve formě nezpracovaného nestrukturovaného textu ve formátu JSON.

3. Odešlete požadavek do koncového bodu vytvořeného během registrace a připojením požadovaného prostředku: analýza mínění, extrakce klíčových frází, rozpoznávání jazyka nebo rozpoznávání pojmenovaných entit.

4. Streamujte nebo ukládejte odpověď místně. V závislosti na požadavku jsou výsledky ve formě skóre mínění, kolekce extrahovaných klíčových frází nebo kódu jazyka.

Výstup se vrátí jako jeden dokument JSON s výsledky pro každý textový dokument, který jste odeslali, na základě ID. Následně můžete analyzovat, vizualizovat nebo kategorizovat výsledky na užitečné přehledy.

Data se neukládají ve vašem účtu. Operace prováděné prostřednictvím rozhraní API pro analýzu textu jsou bezstavové, což znamená poskytnutý text se zpracuje a výsledky vrátí okamžitě.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Analýza textu pro více úrovní prostředí pro programování

Můžete začít používat rozhraní API pro analýzu textu v procesech, a to i v případě, že při programování nemáte hodně zkušeností. Pomocí těchto kurzů se dozvíte, jak můžete použít rozhraní API k analýze textu různými způsoby, aby odpovídaly vaší úrovni prostředí. 

* Vyžaduje se minimální programování:
    * [Extrakce informací v Excelu pomocí Analýza textu a automatizace](tutorials/extract-excel-information.md)
    * [Použití rozhraní API pro analýzu textu a MS Flow k identifikaci mínění komentářů ve skupině Yammeru](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integrace Power BI s rozhraní API pro analýzu textu k analýze názoru zákazníků](tutorials/tutorial-power-bi-key-phrases.md)
* Doporučené programovací prostředí:
    * [Analýza mínění na streamovaných datech s využitím Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Sestavení aplikace v baňce pro překlad textu, analýza mínění a syntetizace řeči](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Podporované jazyky

Tato část byla přesunuta do samostatného článku pro lepší vyhledatelnost. V rozhraní API pro analýzu textu pro tento obsah se můžete podívat na [podporované jazyky](./language-support.md) .

<a name="data-limits"></a>

## <a name="data-limits"></a>Omezení dat

Všechny koncové body rozhraní API pro analýzu textu přijímají data v podobě nezpracovaného textu. Další informace najdete v článku [omezení pro data](concepts/data-limits.md) .

## <a name="unicode-encoding"></a>Kódování Unicode

Rozhraní API pro analýzu textu používá kódování Unicode pro textové vyjádření a kalkulaci počtu znaků. Požadavky je možné odesílat v kódování UTF-8 i UTF-16 bez měřitelných rozdílů v počtu znaků. Body kódu sady Unicode jsou použité jako heuristika délky znaků a jsou považovány za ekvivalentní ve smyslu datového omezení analýzy textu. Pokud použijete [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) k získání počtu znaků, použijete stejnou metodu, kterou používáme k měření velikosti dat.

## <a name="next-steps"></a>Další kroky

+ [Vytvořte prostředek Azure](../cognitive-services-apis-create-account.md) pro analýza textu, abyste získali klíč a koncový bod pro vaše aplikace.

+ Pomocí [rychlého](quickstarts/client-libraries-rest-api.md) startu spustíte odesílání volání rozhraní API. Zjistěte, jak odesílat text, zvolit analýzu a zobrazit výsledky s minimem kódu.

+ Další informace o nových verzích a funkcích najdete [v tématu Co je nového v rozhraní API pro analýzu textu](whats-new.md) .

+ Dig se tento [kurz analýzy mínění](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) pomocí Azure Databricks trochu hlubší.

+ Podívejte se na náš seznam blogových příspěvků a další videa o použití rozhraní API pro analýzu textu s dalšími nástroji a technologiemi na naší [externí stránce obsahu komunity &](text-analytics-resource-external-community.md).
