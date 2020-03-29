---
title: Kognitivní služby a strojové učení
titleSuffix: Azure Cognitive Services
description: Projděte si víc o vztahu Azure Cognitive Services k ostatním nabídkám Azure pro strojové učení.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: diberry
ms.openlocfilehash: cde505e4c95de9b9693a0e9d260d7fa84f3e905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75531475"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services a strojové učení

Služba Cognitive Services poskytuje funkce strojového učení k řešení obecných problémů, jako je analýza textu pro emocionální mínění nebo analýza obrázků za účelem rozpoznání objektů nebo tváří. K používání těchto služeb nepotřebujete speciální znalosti strojového učení nebo datové vědy. 

[Cognitive Services](welcome.md) je skupina služeb, z nichž každá podporuje různé, generalizované možnosti předpovědi. Služby jsou rozděleny do různých kategorií, které vám pomohou najít správnou službu. 

|Kategorie služeb|Účel|
|--|--|
|[Rozhodnutí](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Sestavujte aplikace, které zveřejňují doporučení pro informované a efektivní rozhodování.|
|[Jazyk](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Umožněte svým aplikacím, aby zpracovávaly přirozený jazyk s využitím předem sestavených skriptů, vyhodnocovaly subjektivní hodnocení a naučily se rozpoznávat, co uživatelé chtějí.|
|[Hledat](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Přidejte do svých aplikací rozhraní API pro Vyhledávání Bingu a využijte možnosti pročesávat miliardy webů, obrázků, videí a novinek pomocí jediného volání rozhraní API.|
|[Řeč](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Převádějte řeč na text a text na přirozeně znějící řeč. Překládejte z jednoho jazyka do druhého a používejte možnost ověření a rozpoznání mluvčího.|
|[Obraz](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Využijte možnost rozpoznávat, identifikovat, indexovat a moderovat vaše obrázky, videa a obsah digitálního rukopisu a opatřovat je titulky.|
||||

Služby Cognitive Services používejte v:

* Lze použít zobecněné řešení.
* Přístup k řešení z programovacího rozhraní REST API nebo sady SDK. 

Použijte jiné řešení strojového učení, když:

* Je třeba zvolit algoritmus a je třeba trénovat na velmi konkrétní data.

## <a name="what-is-machine-learning"></a>Co je strojové učení?

Strojové učení je koncept, ve kterém spojujete data a algoritmus, který řeší konkrétní potřeby. Jakmile jsou trénovaná data a algoritmus, výstup je model, který můžete znovu použít s různými daty. Trénovaný model poskytuje přehledy založené na nových datech. 

Proces vytváření systému strojového učení vyžaduje určité znalosti strojového učení nebo datové vědy.

Strojové učení se poskytuje pomocí [produktů a služeb Azure Machine Learning (AML).](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure/machine-learning/studio/context/ml-context)

## <a name="what-is-a-cognitive-service"></a>Co je kognitivní služba?

Služba Cognitive Service poskytuje část nebo všechny součásti v řešení strojového učení: data, algoritmus a trénovaný model. Tyto služby jsou určeny k vyžadování obecných znalostí o vašich datech bez nutnosti zkušeností se strojovým učením nebo datovou vědou. Tyto služby poskytují rozhraní REST API i sady SDK založené na jazyce. V důsledku toho musíte mít znalosti programovacího jazyka k použití služeb.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Jak jsou kognitivní služby a Azure Machine Learning (AML) podobné?

Oba mají konečný cíl použití umělé inteligence (AI) pro zlepšení obchodních operací, i když to, jak to každý poskytuje v příslušných nabídkách, je jiné. 

Obecně platí, že okruhy uživatelů se liší:

* Služby Cognitive Services jsou určeny pro vývojáře bez zkušeností se strojovým učením.
* Azure Machine Learning je šitý na míru pro datové vědce. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Jak se kognitivní služba liší od strojového učení?

Služba Cognitive Service poskytuje trénovaný model pro vás. To přináší data a algoritmus dohromady, k dispozici z ROZHRANÍ REST API nebo SDK. Tuto službu můžete implementovat během několika minut, v závislosti na vašem scénáři.  Služba Cognitive Service poskytuje odpovědi na obecné problémy, jako jsou klíčové fráze v identifikaci textu nebo položky v obrázcích. 

Strojové učení je proces, který obecně vyžaduje delší dobu k úspěšné implementaci. Tento čas se stráví shromažďováním dat, čištěním, transformací, výběrem algoritmů, trénovací modelu a nasazením, abyste se dostali na stejnou úroveň funkcí poskytovaných službou Cognitive Service. Díky strojovému učení je možné poskytnout odpovědi na vysoce specializované a/nebo specifické problémy. Problémy se strojovým učením vyžadují znalost konkrétního předmětu a dat zvažovaného problému a také odborné znalosti v oblasti datové vědy.

## <a name="what-kind-of-data-do-you-have"></a>Jaký druh dat máte?

Cognitive Services, jako skupina služeb, může vyžadovat žádné, některé nebo všechna vlastní data pro trénovaný model. 

### <a name="no-additional-training-data-required"></a>Nejsou vyžadována žádná další trénovací data

Služby, které poskytují plně vyškolený model, lze považovat za _černou skříňku_. Nemusíte vědět, jak fungují nebo jaká data byla použita k jejich trénování. Přenesete data do plně trénovaného modelu, abyste získali předpověď. 

### <a name="some-or-all-training-data-required"></a>Některé nebo všechny údaje o školení

Některé služby umožňují přinést vlastní data a pak trénovat model. To umožňuje rozšířit model pomocí dat a algoritmu služby s vlastními daty. Výstup odpovídá vašim potřebám. Při přinášení vlastních dat může být nutné označit data způsobem specifickým pro službu. Například pokud trénujete model k identifikaci květin, můžete poskytnout katalog květinových obrázků spolu s umístěním květiny v každém obrázku pro trénování modelu. 

Služba vám může _umožnit_ poskytovat data za účelem vylepšení vlastních dat. Služba může _vyžadovat_ poskytnutí dat. 

### <a name="real-time-or-near-real-time-data-required"></a>Požadovaná data v reálném čase nebo téměř v reálném čase

Služba může potřebovat data v reálném čase nebo téměř v reálném čase k vytvoření efektivního modelu. Tyto služby zpracovávají významné množství dat modelu. 

## <a name="service-requirements-for-the-data-model"></a>Požadavky na služby pro datový model

Následující data kategorizuje každou službu podle toho, jaký druh dat povoluje nebo vyžaduje.

|Kognitivní služby|Nejsou vyžadována žádná data školení|Poskytujete některé nebo všechny údaje školení|Sběr dat v reálném čase nebo téměř v reálném čase|
|--|--|--|--|
|[Detektor anomálií](./Anomaly-Detector/overview.md)|x|x|x|
|Vyhledávání pomocí služby Bing |x|||
|[Počítačové vidění](./Computer-vision/Home.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./Custom-Vision-Service/home.md)||x||
|[Tvář](./Face/Overview.md)|x|x||
|[Rozpoznávání formulářů](./form-recognizer/overview.md)||x||
|[Asistivní čtečka](./immersive-reader/overview.md)|x|||
|[Rozpoznávání rukopisu](./Ink-recognizer/overview.md)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizace](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Rozpoznávač reproduktorů](./speaker-recognition/home.md)||x||
|[Převod textu na řeč (TTS)](speech-service/text-to-speech.md)|x|x||
|[Řeč řeči na text (STT)](speech-service/speech-to-text.md)|x|x||
|[Překlad řeči](speech-service/speech-translation.md)|x|||
|[Analýza textu](./text-analytics/overview.md)|x|||
|[Translator Text](./translator/translator-info-overview.md)|x|||
|[Překladateltext - vlastní překladatel](./translator/custom-translator/overview.md)||x||

*Personalizár potřebuje k vyhodnocení vašich zásad a dat pouze školicí data shromážděná službou (protože funguje v reálném čase). Personalizátor nepotřebuje velké historické datové sady pro počáteční nebo dávkové školení. 

## <a name="where-can-you-use-cognitive-services"></a>Kde můžete služby Cognitive Services používat?
 
Služby se používají v libovolné aplikaci, která může provádět volání rest api nebo sdk. Příklady aplikací zahrnují webové stránky, roboty, virtuální nebo smíšenou realitu, desktopové a mobilní aplikace. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Jak souvisí Azure Cognitive Search se službami Cognitive Services?

[Azure Cognitive Search](../search/search-what-is-azure-search.md) je samostatná cloudová vyhledávací služba, která volitelně používá služby Cognitive Services k přidání zpracování image a přirozeného jazyka do úloh indexování. Kognitivní služby jsou vystaveny v Azure Cognitive Search prostřednictvím [integrovaných dovedností,](../search/cognitive-search-predefined-skills.md) které zabalí jednotlivá řešení API. Můžete použít volný prostředek pro návody, ale naplánovat vytvoření a připojení [fakturovatelného zdroje](../search/cognitive-search-attach-cognitive-services.md) pro větší svazky.

## <a name="how-can-you-use-cognitive-services"></a>Jak můžete služby Cognitive Services používat?

Každá služba poskytuje informace o vašich datech. Služby můžete kombinovat dohromady a zřetězit řešení, jako je převod řeči (zvuku) na text, překlad textu do mnoha jazyků a následné získání odpovědí ze znalostní báze pomocí přeložených jazyků. Zatímco služby Cognitive Services lze použít k vytváření inteligentních řešení samostatně, lze je také kombinovat s tradičními projekty strojového učení, které doplňují modely nebo urychlují proces vývoje. 

Služby Cognitive Services, které poskytují exportované modely pro jiné nástroje strojového učení:

|Kognitivní služby|Informace o modelu|
|--|--|
|[Custom Vision](./custom-vision-service/home.md)|[Export](./Custom-Vision-Service/export-model-python.md) pro Tensorflow pro Android, CoreML pro iOS11, ONNX pro Windows ML|

## <a name="learn-more"></a>Další informace

* [Architektura Guide - Jaké jsou produkty strojového učení v Microsoftu?](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Strojové učení – úvod do hloubkového učení vs. strojové učení](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Další kroky

* Vytvořte si účet kognitivní služby na [webu Azure Portal](cognitive-services-apis-create-account.md) nebo pomocí azure [cli](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli).
* Přečtěte si, jak se [ověřit](authentication.md) u služby Cognitive Service.
* Diagnostické [protokolování](diagnostic-logging.md) použijte pro identifikaci a ladění problémů. 
* Nasazení služby Cognitive Service v [kontejneru Dockeru](cognitive-services-container-support.md).
* Udržujte si aktuální informace o [aktualizacích služeb](https://azure.microsoft.com/updates/?product=cognitive-services).
