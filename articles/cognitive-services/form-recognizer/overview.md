---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Nástroj Pro rozpoznávání formulářů služby Azure Cognitive Services umožňuje identifikovat a extrahovat dvojice klíč/hodnota a data tabulky z dokumentů formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6919849d28573ad7388a7f2e317d2b8433f35559
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399456"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Form Recognizer je kognitivní služba, která používá technologii strojového učení k identifikaci a extrahování textu, párů klíčů a hodnot a dat tabulek z dokumentů formuláře. Ingestuje text z formulářů a výstupů strukturovaných dat, která zahrnuje relace v původním souboru. Rychle získáte přesné výsledky, které jsou přizpůsobeny vašemu konkrétnímu obsahu bez náročného ručního zásahu nebo rozsáhlých odborných znalostí v oblasti datových věd. Rozpoznávání formulářů se skládá z vlastních modelů, předem sestaveného modelu příjmu a rozhraní API rozložení. Modely rozpoznávání formulářů můžete volat pomocí rozhraní REST API, abyste snížili složitost a integrovali je do pracovního postupu nebo aplikace.

Nástroj pro rozpoznávání formulářů se skládá z následujících služeb:
* **Vlastní modely** – extrahujte dvojice klíč/hodnota a data tabulky z formulářů. Tyto modely jsou trénované s vlastními daty, takže jsou přizpůsobeny vašim formulářům.
* **Předem sestavený model příjmu** - Extrahujte data z prodejních příjmů v USA pomocí předem sestaveného modelu.
* **Rozhraní API rozložení** – Extrahujte struktury textu a tabulek spolu s jejich souřadnicemi ohraničovacího rámečku z dokumentů.

<!-- add diagram -->

## <a name="custom-models"></a>Vlastní modely

Vlastní modely nástroje pro rozpoznávání formulářů trénují na vlastní data a ke spuštění stačí pouze pět vzorových vstupních formulářů. Trénovaný model může vytvářet strukturovaná data, která zahrnují vztahy v původním dokumentu formuláře. Po trénování modelu jej můžete testovat a přeškolit a případně jej použít ke spolehlivému extrahování dat z více formulářů podle vašich potřeb.

Při trénování vlastních modelů máte následující možnosti: trénování s označenými daty a bez označených dat.

### <a name="train-without-labels"></a>Vlak bez štítků

Ve výchozím nastavení nástroj pro rozpoznávání formulářů používá učení bez dozoru k pochopení rozložení a vztahů mezi poli a položkami ve formulářích. Při odeslání vstupních formulářů algoritmus shlukne formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty ke klíčům a položkám do tabulek. To nevyžaduje ruční označování dat nebo intenzivní kódování a údržbu a doporučujeme nejprve vyzkoušet tuto metodu.

### <a name="train-with-labels"></a>Vlak s etiketami

Když trénujete s označenými daty, model provádí učení pod dohledem, aby extrahoval hodnoty zájmu, pomocí označených formulářů, které zadáte. Výsledkem jsou výkonnější modely a mohou vytvářet modely, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.

Nástroj pro rozpoznávání formulářů používá [rozhraní API rozložení](#layout-api) k naučení očekávaných velikostí a pozic tištěných a ručně psaných textových prvků. Potom používá uživatelem zadané popisky k naučení přidružení klíč/hodnota v dokumentech. Doporučujeme použít pět ručně označených formulářů stejného typu, abyste mohli začít při trénování nového modelu a podle potřeby přidat další data s popiskem ke zlepšení přesnosti modelu.

## <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Nástroj pro rozpoznávání formulářů také obsahuje model pro&mdash;čtení anglických prodejních příjmů ze Spojených států, typu používaného restauracemi, čerpacími stanicemi, maloobchodem a tak dále[(příjem vzorku).](./media/contoso-receipt-small.png) Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkovi, částky daní a součty a další. Kromě toho je předem sestavený model příjmu trénován tak, aby rozpoznal a vrátil veškerý text na účtence.

## <a name="layout-api"></a>Rozhraní API rozložení

Nástroj pro rozpoznávání formulářů může také extrahovat strukturu textu a tabulky (čísla řádků a sloupců přidružená k textu) pomocí optického rozpoznávání znaků s vysokým rozlišením (OCR).

## <a name="get-started"></a>Začínáme

Chcete-li začít extrahovat data z formulářů, postupujte podle rychlého začátku. Doporučujeme používat bezplatnou službu, když se učíte technologii. Nezapomeňte, že počet volných stránek je omezen na 500 za měsíc.

* Vlastní - trénování modelu do formulářů
  * Vlak bez štítků
    * [Úvodní příručka: Trénování modelu rozpoznávání formulářů a extrahování dat formuláře pomocí rozhraní REST API s cURL](quickstarts/curl-train-extract.md)
    * [Úvodní příručka: Trénování modelu rozpoznávání formulářů a extrahování dat formuláře pomocí rozhraní REST API s Pythonem](quickstarts/python-train-extract.md)
  * Vlak s etiketami
    * [Trénování modelu rozpoznávání formulářů pomocí popisků pomocí nástroje pro označování vzorků](quickstarts/label-tool.md)
    * [Trénování modelu rozpoznávání formulářů s popisky pomocí rozhraní REST API a Pythonu](quickstarts/python-labeled-data.md)
* Předem sestavené příjmy - výpis dat z prodejních příjmů v USA
  * [Úvodní příručka: Extrahujte data účtenek pomocí cURL](quickstarts/curl-receipts.md)
  * [Úvodní příručka: Extrahování dat účtenek pomocí Pythonu](quickstarts/python-receipts.md)
* Rozložení – extrahování struktury textu a tabulky z formulářů
  * [Úvodní příručka: Extrahování dat rozložení pomocí Pythonu](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>Kontrola rest API

Následující api použijete k trénování modelů a extrahování strukturovaných dat z formulářů.

|Název |Popis |
|---|---|
| **Vlastní model vlaku**| Trénování nového modelu k analýze formulářů pomocí pěti formulářů stejného typu. Nastavte parametr _useLabelFile_ tak, aby `true` trénoval s ručně označenými daty. |
| **Analyzovat formulář** |Analyzujte jeden dokument předaný jako datový proud a extrahujte text, dvojice klíč/hodnota a tabulky z formuláře pomocí vlastního modelu.  |
| **Analyzovat příjem** |Analyzujte jeden doklad příjmu a extrahujte klíčové informace a další text účtenky.|
| **Analyzovat rozložení** |Analyzujte rozložení formuláře a extrahujte strukturu textu a tabulky.|

Další informace najdete v [referenční dokumentaci rozhraní REST API.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [Co je nového,](./whats-new.md) kde se dozvíte o nedávných změnách.

## <a name="input-requirements"></a>Vstupní požadavky
### <a name="custom-model"></a>Vlastní model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Vstupní požadavky pro model příjmu se mírně liší.

* Formát musí být JPEG, PNG, BMP, PDF (text nebo naskenovaný) nebo TIFF.
* Velikost souboru musí být menší než 20 MB.
* Rozměry obrazu musí být mezi 50 x 50 pixelů a 10000 x 10000 pixelů.
* Rozměry PDF musí být maximálně 17 x 17 palců, což odpovídá formátu papíru Legal nebo A3 a menší.
* Pro PDF a TIFF jsou zpracovány pouze prvních 200 stránek (s bezplatným předplatným úrovně jsou zpracovány pouze první dvě stránky).

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení

Tato služba je nabízena jako [náhled](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) služby Azure v rámci [podmínek online služby](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Stejně jako u všech služeb cognitive, vývojáři pomocí služby nástroje pro rozpoznávání formulářů by měli být vědomi zásad společnosti Microsoft na zákaznická data. Další informace najdete na [stránce Služby Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v Centru zabezpečení společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

Chcete-li začít pracovat s [api nástroje PRO Rozpoznávání formulářů ,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)proveďte rychlý [start.](quickstarts/curl-train-extract.md)
