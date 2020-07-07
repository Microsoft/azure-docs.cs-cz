---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Nástroj pro rozpoznávání formulářů Azure Cognitive Services umožňuje identifikovat a extrahovat páry klíč/hodnota a tabulková data z dokumentů formuláře.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: f5d0e9a851c41fa88461ed790ad3fe4e89d0fa21
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957133"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nástroj pro rozpoznávání formulářů Azure je výtahová služba, která pomocí technologie strojového učení identifikuje a extrahuje z dokumentů formuláře páry textů, dvojic klíč/hodnota a tabulková data. Ingestuje text z formulářů a vypíše strukturovaná data, která zahrnují relace v původním souboru. Rychle získáte přesné výsledky, které jsou přizpůsobené vašemu konkrétnímu obsahu bez nutnosti ručních zásahů nebo rozsáhlých znalostí z oblasti datové vědy. Nástroj pro rozpoznávání formulářů se skládá z vlastních modelů, předem připraveného modelu příjemek a rozhraní API pro rozložení. Můžete volat modely pro rozpoznávání formulářů pomocí REST API ke snížení složitosti a jejich integraci do pracovního postupu nebo aplikace.

Nástroj pro rozpoznávání formulářů se skládá z následujících služeb:
* **Vlastní modely** – z formulářů extrahuje páry klíč/hodnota a tabulková data. Tyto modely jsou vyškolené s vašimi vlastními daty, takže jsou přizpůsobené vašim formám.
* **Model předem vytvořeného příjmu** – extrakce dat z prodejních příjmů v USA pomocí předem připraveného modelu.
* **Rozhraní API pro rozložení** – rozbalí textové a tabulkové struktury spolu s jejich souřadnicemi ohraničovacího rámečku z dokumentů.

<!-- add diagram -->

## <a name="custom-models"></a>Vlastní modely

Vlastní modely pro rozpoznávání formulářů se spouštějí na vaše vlastní data a stačí, abyste mohli začít jenom pět vzorových vstupních formulářů. Vyškolený model může výstupovat strukturovaná data, která obsahují vztahy v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

Při výuce vlastních modelů máte k dispozici následující možnosti: školení s popisky dat a bez označení dat.

### <a name="train-without-labels"></a>Výuka bez popisků

Ve výchozím nastavení používá nástroj pro rozpoznávání formulářů nepod dohledem informace o rozložení a vztazích mezi poli a položkami ve formulářích. Když odešlete vstupní formuláře, algoritmus clusteruje formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty k klíčům a záznamům k tabulkám. To nevyžaduje ruční označování dat nebo psaní kódu a údržby a doporučujeme tuto metodu vyzkoušet jako první.

### <a name="train-with-labels"></a>Výuka s popisky

Při výuce s povzorovými daty model předává pod dohledem učení o extrakci hodnot, které vás zajímají, pomocí popisků, které zadáte. Výsledkem je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.

Nástroj pro rozpoznávání formulářů používá [rozhraní API pro rozložení](#layout-api) a zjišťuje očekávané velikosti a pozice vytištěných a ručně psaných textových prvků. Pak pomocí uživatelem zadaných popisků zjistí přidružení klíč/hodnota v dokumentech. Doporučujeme použít pět ručně označených forem stejného typu, abyste mohli začít při výuce nového modelu a přidávat další označená data, aby se zlepšila přesnost modelu.

## <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Nástroj pro rozpoznávání formulářů obsahuje také model pro čtení prodejních příjmů v angličtině z USA &mdash; typ používaný v restauracích, čerpacích stanicích, maloobchodě atd. ([příjem vzorků](./media/contoso-receipt-small.png)). Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkech, množství daní a součtů a další. Předem sestavený model příjmu je navíc vyškolen pro rozpoznání a vrácení veškerého textu na účtence.

## <a name="layout-api"></a>Rozhraní API pro rozložení

Nástroj pro rozpoznávání formulářů také může extrahovat textovou a tabulkovou strukturu (čísla řádků a sloupců přidružených k tomuto textu) pomocí optického rozpoznávání znaků (OCR) s vysokým rozlišením.

## <a name="get-started"></a>Začínáme

Pomocí rychlého startu můžete začít extrahovat data z formulářů. Při učení technologie doporučujeme používat bezplatnou službu. Mějte na paměti, že počet bezplatných stránek je omezený na 500 za měsíc.

* [Rychlý Start knihovny klienta](./quickstarts/client-library.md) (všechny jazyky, více scénářů)
* Rychlé starty webového uživatelského rozhraní
  * [Výuka pomocí popisků – vzorový Nástroj pro označování](quickstarts/label-tool.md)
* Rychlé starty REST
  * Výuka vlastních modelů a extrahování dat formuláře
    * [Výuka bez popisků – kudrlinkou](quickstarts/curl-train-extract.md)
    * [Výuka bez popisků – Python](quickstarts/python-train-extract.md)
    * [Výuka s popisky – Python](quickstarts/python-labeled-data.md)
  * Extrakce dat z prodejních příjmů z USA
    * [Extrahovat data příjmu – kudrlinkou](quickstarts/curl-receipts.md)
    * [Extrakce dat pro příjem – Python](quickstarts/python-receipts.md)
  * Extrakce textu a struktury tabulky z formulářů
    * [Extrakce dat rozložení – Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Kontrola rozhraní REST API

Pomocí následujících rozhraní API můžete vyškolit modely a extrahovat strukturovaná data z formulářů.

|Name |Popis |
|---|---|
| **Vlastní model výuky**| Vytvořte nový model pro analýzu formulářů s použitím pěti forem stejného typu. Nastavte parametr _useLabelFile_ na `true` Výukový program s ručně označenými daty. |
| **Analyzovat formulář** |Analyzujte jeden dokument předaný jako datový proud pro extrakci textu, párů klíč/hodnota a tabulek z formuláře pomocí vlastního modelu.  |
| **Analyzovat příjem** |Analyzujte jeden příjmový doklad pro extrakci klíčových informací a dalšího textu příjmu.|
| **Analyzovat rozložení** |Umožňuje analyzovat rozložení formuláře pro extrakci textu a struktury tabulky.|

Další informace najdete v [referenční dokumentaci k REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

## <a name="input-requirements"></a>Požadavky na vstup
### <a name="custom-model"></a>Vlastní model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Vstupní požadavky pro model příjemky se mírně liší.

* Formát musí být JPEG, PNG, PDF (text nebo naskenovaný) nebo TIFF.
* Velikost souboru musí být menší než 20 MB.
* Rozměry obrázku musí být mezi 50 × 50 pixelů a 10000 × 10000 pixelů.
* Rozměry PDF musí být maximálně 17 × 17 palců, které odpovídají zákonným nebo a3 velikosti papíru a menšímu.
* Pro PDF a TIFF se zpracovávají jenom první 200 stránky (s předplatným úrovně Free, zpracovávají se jenom první dvě stránky).

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu pro rozpoznávání formulářů, znát zásady Microsoftu u zákaznických dat. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start](quickstarts/curl-train-extract.md) , abyste mohli začít s [rozhraními API pro rozpoznávání formulářů](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).
