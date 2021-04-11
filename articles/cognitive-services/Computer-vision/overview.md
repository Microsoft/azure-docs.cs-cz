---
title: Co je počítačové zpracování obrazu?
titleSuffix: Azure Cognitive Services
description: Služba Počítačové zpracování obrazu poskytuje přístup k pokročilým algoritmům pro zpracování imagí a vracení informací.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: počítačové zpracování obrazu, aplikace počítačové vidění, Služba Computer visioning Service
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286130"
---
# <a name="what-is-computer-vision"></a>Co je počítačové zpracování obrazu?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Služba Počítačové zpracování obrazu v Azure vám poskytuje přístup k pokročilým algoritmům, které zpracovávají obrázky a vracejí informace na základě vizuálních funkcí, které vás zajímají. 

| Služba|Popis|
|---|---|
|[optické rozpoznávání znaků (OCR),](overview-ocr.md)|Služba optického rozpoznávání znaků (OCR) extrahuje text z obrázků. Nové rozhraní API pro čtení můžete použít k extrakci vytištěného a rukopisného textu z fotek a dokumentů. Používá modely založené na hloubkovém učení a pracuje s textem na různých površích a na pozadí. Patří mezi ně obchodní dokumenty, faktury, účtenky, plakáty, vizitky, dopisy a tabule. Rozhraní API pro rozpoznávání OCR podporují extrakci vytištěného textu v [několika jazycích](./language-support.md). Začněte tím, že přejdete k [rychlému startu rozpoznávání OCR](quickstarts-sdk/client-library.md) .|
|[Analýza obrázků](overview-image-analysis.md)| Služba analýza obrázků extrahuje mnoho vizuálních funkcí z obrázků, jako jsou objekty, plošky, obsah pro dospělé a automaticky vygenerované textové popisy. Začněte tím, že budete postupovat podle pokynů pro [rychlý Start pro analýzu imagí](quickstarts-sdk/image-analysis-client-library.md) .|
| [Prostorová analýza](intro-to-spatial-analysis-public-preview.md)| Služba prostorové analýzy analyzuje přítomnost a pohyb lidí na informačním kanálu videa a vytváří události, na které ostatní systémy mohou reagovat. Začněte tím, že nainstalujete [kontejner prostorové analýzy](spatial-analysis-container.md) .|

## <a name="computer-vision-for-digital-asset-management"></a>Počítačové zpracování obrazu pro správu digitálních prostředků

Počítačové zpracování obrazu může výkon mnoha scénářů správy digitálních prostředků (přehradit). Přehradní je obchodní proces organizace, ukládání a načítání multimediálních prostředků a správu digitálních práv a oprávnění. Společnost může například chtít seskupit a identifikovat obrázky na základě viditelných log, plošek, objektů, barev a tak dále. Nebo můžete chtít automaticky [vygenerovat titulky pro obrázky](./Tutorials/storage-lab-tutorial.md) a připojit klíčová slova, aby je bylo možné prohledávat. Pro přehradní řešení all-in-One pomocí Cognitive Services, Azure Kognitivní hledání a inteligentního generování sestav si Projděte [příručku akcelerátoru řešení](https://github.com/Azure-Samples/azure-search-knowledge-mining) pro vyhledávání znalostí na GitHubu. Další příklady přehrad naleznete v tématu [počítačové zpracování obrazu – úložiště šablon řešení](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) .

## <a name="image-requirements"></a>Požadavky image

Počítačové zpracování obrazu dokáže analyzovat obrázky, které splňují následující požadavky:

- Obrázek musí být ve formátu JPEG, PNG, GIF nebo BMP.
- Velikost souboru obrázku musí být menší než 4 megabajty (MB).
- Rozměry obrázku musí být větší než 50 × 50 pixelů.
  - Pro rozhraní API pro čtení musí být rozměry obrázku mezi 50 x 50 a 10000 × 10000 pixelů.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Počítačové zpracování obrazu službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Postupujte podle rychlého startu a implementujte a spusťte službu v preferovaném vývojovém jazyce.

* [Rychlý Start: optické rozpoznávání znaků (OCR)](quickstarts-sdk/client-library.md)
* [Rychlý Start: analýza obrázku](quickstarts-sdk/image-analysis-client-library.md)
* [Rychlý Start: kontejner prostorových analýz](spatial-analysis-container.md)
