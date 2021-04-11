---
title: Cognitive Services a Machine Learning
titleSuffix: Azure Cognitive Services
description: Projděte si víc o vztahu Azure Cognitive Services k ostatním nabídkám Azure pro strojové učení.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: 7946bc23f766eaf99860fa764ee7b542036b5109
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075776"
---
# <a name="cognitive-services-and-machine-learning"></a>Cognitive Services a strojové učení

Cognitive Services poskytuje možnosti strojového učení, které řeší Obecné problémy, jako je například analýza textu pro emocionálních mínění nebo analýza obrázků pro rozpoznávání objektů a tváře. Abyste mohli s těmito službami pracovat, nepotřebujete žádné speciální vědomosti o strojovém učení nebo datových vědách. 

[Cognitive Services](./what-are-cognitive-services.md) je skupina služeb, z nichž každá podporuje různé Obecné možnosti předpovědi. Služby jsou rozdělené do různých kategorií, které vám pomůžou najít správnou službu. 

|Kategorie služby|Účel|
|--|--|
|[Rozhodnutí](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|Sestavujte aplikace, které zveřejňují doporučení pro informované a efektivní rozhodování.|
|[Jazyk](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|Umožněte svým aplikacím, aby zpracovávaly přirozený jazyk s využitím předem sestavených skriptů, vyhodnocovaly subjektivní hodnocení a naučily se rozpoznávat, co uživatelé chtějí.|
|[Hledání](https://azure.microsoft.com/services/cognitive-services/directory/search/)|Do aplikací můžete přidat rozhraní API pro vyhledávání Bingu a využívat možnost pročesávat miliardy webových stránek, obrázků, videí a novinek pomocí jediného volání rozhraní API.|
|[Speech](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|Převádějte řeč na text a text na přirozeně znějící řeč. Překládejte z jednoho jazyka do druhého a používejte možnost ověření a rozpoznání mluvčího.|
|[Obraz](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|Využijte možnost rozpoznávat, identifikovat, indexovat a moderovat vaše obrázky, videa a obsah digitálního rukopisu a opatřovat je titulky.|

Použijte Cognitive Services, když:

* Může používat zobecněné řešení.
* Přístup k řešení z programovacího REST API nebo sady SDK. 

Použijte jiné řešení strojového učení:

* Je nutné zvolit algoritmus a potřebovat vyškolit velmi specifická data.

## <a name="what-is-machine-learning"></a>Co je strojové učení?

Machine Learning je koncept, ve kterém spojíte data a algoritmus, abyste mohli určitou potřebu vyřešit. Po vyškolení dat a algoritmu je výstupem model, který můžete znovu použít s různými daty. Trained model poskytuje přehledy na základě nových dat. 

Proces sestavování systému Machine Learning vyžaduje znalost strojového učení nebo vědeckého zpracování dat.

Machine Learning se poskytuje pomocí [Azure Machine Learning (AML) produktů a služeb](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context).

## <a name="what-is-a-cognitive-service"></a>Co je služba rozpoznávání?

Služba rozpoznávání poskytuje součást nebo všechny komponenty v řešení strojového učení: data, algoritmus a trained model. Tyto služby mají vyžadovat obecné znalosti o vašich datech, aniž by bylo potřeba mít zkušenosti s Machine Learningem nebo datovou vědy. Tyto služby poskytují REST API (s) i sady SDK založené na jazyku. V důsledku toho musíte mít znalosti programovacího jazyka pro používání služeb.

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>Jak jsou podobné Cognitive Services a Azure Machine Learning (AML)?

Oba mají konečný cíl použít umělou Intelligence (AI) k vylepšení obchodních operací, i když každý z nich poskytuje příslušné nabídky. 

Obecně platí, že se cílové skupiny liší:

* Cognitive Services jsou vývojáři bez prostředí strojového učení.
* Azure Machine Learning je přizpůsobená pro odborníky přes data. 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>Jak se služba pro rozpoznávání liší od strojového učení?

Služba pro rozpoznávání vám poskytuje trained model. To přináší data a algoritmus dohromady, k dispozici z REST API nebo sady SDK. V závislosti na vašem scénáři můžete tuto službu implementovat během několika minut.  Služba rozpoznávání poskytuje odpovědi na Obecné problémy, jako jsou klíčové fráze v identifikaci textu nebo položky v obrázcích. 

Machine Learning je proces, který obvykle vyžaduje delší dobu k úspěšnému provedení implementace. Tato doba se vybírá na shromažďování dat, čištění, transformaci, výběru algoritmů, výuce modelů a nasazení, aby se dosáhlo stejné úrovně funkčnosti, jaké poskytuje služba pro rozpoznávání. Pomocí strojového učení je možné poskytnout odpovědi na vysoce specializované nebo specifické problémy. Problémy strojového učení vyžadují znalost konkrétního předmětu a dat, která jsou v souladu s problémem, a také odbornosti v oblasti datové vědy.

## <a name="what-kind-of-data-do-you-have"></a>Jaký druh dat máte?

Cognitive Services jako skupina služeb může pro školený model vyžadovat žádná, některá nebo všechna vlastní data. 

### <a name="no-additional-training-data-required"></a>Nevyžadují se žádné další školicí údaje.

Služby, které poskytují plně vyškolený model, se dají považovat za _neprůhledné pole_. Nemusíte znát, jak fungují nebo jaká data se použila k jejich školení. Data přenesete do plně vyškolených modelů, abyste získali předpověď. 

### <a name="some-or-all-training-data-required"></a>Některá nebo všechna nutná školicí data

Některé služby vám umožňují přenášet vlastní data a pak vytvořit model. To vám umožní tento model roztáhnout pomocí dat a algoritmu služby s vašimi vlastními daty. Výstup odpovídá vašim potřebám. Když přinesete vlastní data, může být nutné označit data způsobem specifickým pro danou službu. Pokud například provedete výuku modelu, který identifikuje květiny, můžete poskytnout katalog květinových obrázků spolu s umístěním květu v jednotlivých obrázcích pro výuku modelu. 

Služba _vám může poskytnout data_ pro vylepšení vlastních dat. Služba může _vyžadovat_ poskytnutí dat. 

### <a name="real-time-or-near-real-time-data-required"></a>Vyžaduje se data v reálném čase nebo téměř v reálném čase.

Služba může pro sestavení efektivního modelu potřebovat data v reálném čase nebo téměř v reálném čase. Tyto služby zpracovávají významné množství dat modelu. 

## <a name="service-requirements-for-the-data-model"></a>Požadavky na služby pro datový model

Následující data kategorizují každou službu podle druhu dat, která povoluje nebo vyžaduje.

|Služba rozpoznávání|Nevyžadují se žádné školicí údaje.|Poskytnete některá nebo všechna školicí data.|Shromažďování dat v reálném čase nebo téměř v reálném čase|
|--|--|--|--|
|[Detektor anomálií](./Anomaly-Detector/overview.md)|x|x|x|
|Vyhledávání Bingu |x|||
|[Počítačové zpracování obrazu](./computer-vision/overview.md)|x|||
|[Content Moderator](./Content-Moderator/overview.md)|x||x|
|[Custom Vision](./custom-vision-service/overview.md)||x||
|[Rozpoznávání tváře](./Face/Overview.md)|x|x||
|[Rozpoznávání formulářů](./form-recognizer/overview.md)||x||
|[Asistivní čtečka](./immersive-reader/overview.md)|x|||
|[Rozpoznávání rukopisu](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[Language Understanding (LUIS)](./LUIS/what-is-luis.md)||x||
|[Personalizace](./personalizer/what-is-personalizer.md)|znak|znak|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[Nástroj pro rozpoznávání mluvčího](./speech-service/speaker-recognition-overview.md)||x||
|[Převod textu na řeč hlasu (TTS)](speech-service/text-to-speech.md)|x|x||
|[Převod řeči na text (STT)](speech-service/speech-to-text.md)|x|x||
|[Překlad řeči](speech-service/speech-translation.md)|x|||
|[Analýza textu](./text-analytics/overview.md)|x|||
|[Translator](./translator/translator-info-overview.md)|x|||
|[Překladatel – vlastní Překladatel](./translator/custom-translator/overview.md)||x||

* Přizpůsobený modul potřebuje jenom školicí data shromážděná službou (stejně jako v reálném čase) k vyhodnocení vašich zásad a dat. Přizpůsobování nepotřebuje velké historické datové sady pro školení předem nebo Batch. 

## <a name="where-can-you-use-cognitive-services"></a>Kde můžete použít Cognitive Services?
 
Služby se používají v jakékoli aplikaci, která může provádět REST API (s) nebo volání sady SDK. Mezi příklady aplikací patří weby, roboty, virtuální nebo smíšené reality, stolní a mobilní aplikace. 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Jak se Azure Kognitivní hledání týkají Cognitive Services?

[Azure kognitivní hledání](../search/search-what-is-azure-search.md) je samostatná cloudová vyhledávací služba, která volitelně používá Cognitive Services k přidání image a zpracování přirozeného jazyka pro indexování úloh. Cognitive Services se zveřejňuje v Azure Kognitivní hledání prostřednictvím [integrovaných dovedností](../search/cognitive-search-predefined-skills.md) , které zabalí jednotlivá rozhraní API. Pro návody můžete použít bezplatný prostředek, ale Naplánujte vytváření a připojování [Fakturovatelné prostředku](../search/cognitive-search-attach-cognitive-services.md) pro větší objemy.

## <a name="how-can-you-use-cognitive-services"></a>Jak můžete použít Cognitive Services?

Každá služba poskytuje informace o vašich datech. Vzájemně můžete kombinovat služby a zřetězit řešení, jako je převod řeči (zvuk) na text, překlad textu do mnoha jazyků a následné použití přeložených jazyků k získání odpovědí ze znalostní báze. I když se Cognitive Services dá použít k vytváření inteligentních řešení na vlastní, můžou se taky kombinovat s tradičními projekty strojového učení, které doplňují modely nebo urychlují proces vývoje. 

Cognitive Services, které poskytují exportované modely pro jiné nástroje pro strojové učení:

|Služba rozpoznávání|Informace o modelu|
|--|--|
|[Custom Vision](./custom-vision-service/overview.md)|[Export](./custom-vision-service/export-model-python.md) pro Tensorflow pro Android, CoreML pro IOS11, ONNX pro Windows ml|

## <a name="learn-more"></a>Další informace

* [Průvodce architekturou – Jaké jsou produkty pro strojové učení v Microsoftu?](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [Machine Learning – Úvod do hloubkového učení vs. Machine Learning](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>Další kroky

* Vytvořte účet služby rozpoznávání v [Azure Portal](cognitive-services-apis-create-account.md) nebo pomocí [Azure CLI](./cognitive-services-apis-create-account-cli.md).
* Přečtěte si, jak [ověřit](authentication.md) službu pro rozpoznávání.
* Pro identifikaci a ladění problému použijte [protokolování diagnostiky](diagnostic-logging.md) . 
* Nasaďte službu pro rozpoznávání do [kontejneru](cognitive-services-container-support.md)Docker.
* Udržujte si [aktualizace služby](https://azure.microsoft.com/updates/?product=cognitive-services)v aktuálním stavu.
