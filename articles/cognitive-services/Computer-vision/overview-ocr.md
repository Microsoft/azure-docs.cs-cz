---
title: Co je optické rozpoznávání znaků?
titleSuffix: Azure Cognitive Services
description: Služba optického rozpoznávání znaků (OCR) extrahuje viditelný text v obrázku a vrátí jej jako strukturované řetězce.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3fff9f4bd34fc1defdb50f2eefbc8ac1f39b46af
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287209"
---
# <a name="what-is-optical-character-recognition"></a>Co je optické rozpoznávání znaků?

Služba optického rozpoznávání znaků (OCR) umožňuje extrahovat vytištěný nebo rukou psaný text z obrázků, například fotografie licenčních desek nebo kontejnerů se sériovými čísly a také z dokumentů &mdash; faktury, faktur, finančních sestav, článků a dalších. Používá modely založené na hloubkovém učení a pracuje s textem na různých površích a na pozadí.

Rozhraní API pro rozpoznávání OCR podporují extrakci vytištěného textu v [několika jazycích](./language-support.md). Začněte pomocí [rychlého](./quickstarts-sdk/client-library.md) startu.

![Ukázky OCR](./Images/ocr-demo.gif)

Tato dokumentace obsahuje následující typy článků:
* [Rychlé starty](./quickstarts-sdk/client-library.md) jsou podrobné pokyny, které vám umožní volat službu a získat výsledky v krátké době. 
* [Příručky](./Vision-API-How-to-Topics/call-read-api.md) návody obsahují pokyny k používání služby v konkrétnějším nebo přizpůsobeném způsobu.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Podporované jazyky
Rozhraní API pro optické rozpoznávání znaků podporují celkem 73 jazyků pro text stylu tisku. Podívejte se na úplný seznam [jazyků podporovaných rozpoznáváním OCR](./language-support.md#optical-character-recognition-ocr). Rukopisné optické rozpoznávání znaků je podporováno výhradně pro angličtinu.

## <a name="input-requirements"></a>Požadavky na vstup

Volání **pro čtení** bere jako vstup obrázky a dokumenty. Mají následující požadavky:

* Podporované formáty souborů: JPEG, PNG, BMP, PDF a TIFF
* Pro soubory PDF a TIFF se zpracují až 2000 stránek (jenom první dvě stránky pro bezplatnou úroveň).
* Velikost souboru musí být menší než 50 MB (4 MB pro úroveň Free) a rozměry aspoň 50 x 50 pixelů a maximálně 10000 x 10000 pixelů. 
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají právním nebo a3 velikosti papíru a menšímu.

## <a name="read-api"></a>Rozhraní API pro čtení 

[Rozhraní API pro počítačové zpracování obrazu Ready](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) je nejnovější technologie OCR Azure ([naučíme se, co je nového](./whats-new.md)), která extrahuje vytištěný text (v několika jazycích), psaný text (jenom v angličtině), číslice a symboly měny z obrázků a vícestránkové dokumenty PDF. Je optimalizovaná pro extrakci textu z textu s velkým obrázkem a vícestránkové dokumenty PDF se smíšenými jazyky. Podporuje detekci tiskového i rukopisného textu ve stejném obrázku nebo dokumentu.

![Jak optické rozpoznávání znaků převede obrázky a dokumenty na strukturovaný výstup s extrahovaným textem](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Použití cloudového rozhraní API nebo nasazení v místním systému
Cloudová rozhraní API pro čtení 3. x jsou upřednostňovanou možností pro většinu zákazníků kvůli snadné integraci a rychlé produktivitě mimo pole. Azure a služba Počítačové zpracování obrazu obsluhují požadavky na škálování, výkon, zabezpečení dat a dodržování předpisů a přitom se soustředit na splnění potřeb vašich zákazníků.

Pro místní nasazení vám [kontejner pro čtení Docker (Preview)](./computer-vision-how-to-install-containers.md) umožňuje nasadit nové možnosti optického rozpoznávání OCR ve vlastním místním prostředí. Kontejnery jsou skvělé pro splnění určitých požadavků na zabezpečení a zásady správného řízení dat.

## <a name="ocr-api"></a>ROZHRANÍ API PRO OPTICKÉ ROZPOZNÁVÁNÍ ZNAKŮ

Starší verze [rozhraní API pro rozpoznávání OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) používá starší model rozpoznávání, podporuje jenom image a provádí synchronní operace, která se zjištěným textem okamžitě vrátí. Seznam podporovaných jazyků najdete v tématu [podporované jazyky](./language-support.md#optical-character-recognition-ocr) ve sloupci OCR.

## <a name="recognizetext-api"></a>Rozhraní API pro RecognizeText

> [!WARNING]
> RecognizeText 2,0 operací se už nepoužívá, a to ve prospěch nového [rozhraní API pro čtení](#read-api) , které je popsané v tomto článku. počítačové zpracování obrazu Stávající zákazníci by měli [Přejít na použití operací čtení](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech Cognitive Services by měli vývojáři, kteří používají Počítačové zpracování obrazu službu, znát zásady Microsoftu pro zákaznická data. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

- Začněte s [REST APIem a rychlými starty knihovny pro rozpoznávání a klienta](./quickstarts-sdk/client-library.md).
- Přečtěte si o [REST API pro čtení 3,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Přečtěte si o [REST API verze Public Preview pro čtení 3,2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) s podporou celkem 73 jazyků.
