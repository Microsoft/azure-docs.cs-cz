---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání formulářů Azure umožňuje identifikovat a extrahovat páry klíč/hodnota a tabulková data z dokumentů formuláře a také extrahovat hlavní informace z prodejních příjmů a vizitek.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: automatizované zpracování dat, zpracování dokumentů, automatizované zadávání dat, zpracování formulářů
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318955"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nástroj pro rozpoznávání formulářů Azure je softwarová služba, která umožňuje sestavovat automatizovaný software pro zpracování dat pomocí technologie strojového učení. Identifikujte a extrahujte páry text, klíč/hodnota a tabulková data z formuláře &mdash; . Tato služba obsahuje výstup strukturovaných dat, která zahrnují relace v původním souboru. Rychle získáte přesné výsledky, které jsou přizpůsobené vašemu konkrétnímu obsahu bez nutnosti ručních zásahů nebo rozsáhlých znalostí z oblasti datové vědy. Použijte nástroj pro rozpoznávání formulářů k automatizaci zadávání dat ve vašich aplikacích.

Nástroj pro rozpoznávání formulářů zahrnuje vlastní modely zpracování dokumentů, předem připravené modely pro příjem a obchodní karty a rozhraní API pro rozložení. Můžete volat modely pro rozpoznávání formulářů pomocí REST API nebo sad SDK klientské knihovny, abyste snížili složitost a mohli je integrovat do pracovního postupu nebo aplikace.

Nástroj pro rozpoznávání formulářů se skládá z následujících služeb:
* **[Vlastní modely](#custom-models)** – z formulářů extrahuje páry klíč/hodnota a tabulková data. Tyto modely jsou vyškolené s vašimi vlastními daty, takže jsou přizpůsobené vašim formám.
* Předem **[připravené modely](#prebuilt-models)** – extrakce dat z jedinečných typů formulářů pomocí předem sestavených modelů. Aktuálně dostupné jsou předem připravené modely pro prodejní příjemky a obchodní karty v angličtině.
* **[Rozhraní API pro rozložení](#layout-api)** – rozbalí textové a tabulkové struktury spolu s jejich souřadnicemi ohraničovacího rámečku z dokumentů.

## <a name="custom-models"></a>Vlastní modely

Vlastní modely pro rozpoznávání formulářů se spouštějí na vaše vlastní data a stačí, abyste mohli začít jenom pět vzorových vstupních formulářů. Model zpracování dokumentů s příškolením může vyvýstupovat strukturovaná data, která obsahují relace v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

Při výuce vlastních modelů máte k dispozici následující možnosti: školení s popisky dat a bez označení dat.

### <a name="train-without-labels"></a>Výuka bez popisků

Ve výchozím nastavení používá nástroj pro rozpoznávání formulářů nepod dohledem informace o rozložení a vztazích mezi poli a položkami ve formulářích. Když odešlete vstupní formuláře, algoritmus clusteruje formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty k klíčům a záznamům k tabulkám. To nevyžaduje ruční označování dat nebo psaní kódu a údržby a doporučujeme tuto metodu vyzkoušet jako první.

Tipy k shromažďování školicích dokumentů najdete v tématu [Vytvoření školicích dat sady](./build-training-data-set.md) .

### <a name="train-with-labels"></a>Výuka s popisky

Při výuce s povzorovými daty model předává pod dohledem učení o extrakci hodnot, které vás zajímají, pomocí popisků, které zadáte. Výsledkem je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.

Nástroj pro rozpoznávání formulářů používá [rozhraní API pro rozložení](#layout-api) a zjišťuje očekávané velikosti a pozice vytištěných a ručně psaných textových prvků. Pak pomocí uživatelem zadaných popisků zjistí přidružení klíč/hodnota v dokumentech. Doporučujeme použít pět ručně označených forem stejného typu, abyste mohli začít při výuce nového modelu a přidávat další označená data, aby se zlepšila přesnost modelu.

## <a name="prebuilt-models"></a>Předem připravené modely

Nástroj pro rozpoznávání formulářů obsahuje také předem připravené modely pro automatizované zpracování dat jedinečných typů formulářů.

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Předem sestavený model příjmu se používá pro čtení prodejních příjmů z Austrálie, Kanady, České Británie, Indie a USA &mdash; typu používaného v restauracích, čerpacích stanicích, maloobchodním prodeji a tak dále. Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkech, množství daní, položek na řádku, součty a další. Předem sestavený model příjmu je navíc vyškolen pro rozpoznání a vrácení veškerého textu na účtence. Další informace najdete v koncepční příručce pro [příjem](./concept-receipts.md) .

![Ukázka účtenky](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Model předdefinovaných vizitek

Model vizitek vám umožňuje extrahovat v angličtině informace, jako je jméno osoby, název úlohy, adresa, e-mail, společnost a telefonní číslo z obchodních karet. Další informace najdete v koncepční příručce pro [obchodní karty](./concept-business-cards.md) .

![Ukázka vizitky](./media/business-card-english.jpg)

## <a name="layout-api"></a>Rozhraní API pro rozložení

Nástroj pro rozpoznávání formulářů také může extrahovat textovou a tabulkovou strukturu (čísla řádků a sloupců přidružených k tomuto textu) pomocí optického rozpoznávání znaků (OCR) s vysokým rozlišením.

## <a name="get-started"></a>Začínáme

Pomocí rychlého startu můžete začít extrahovat data z formulářů. Při učení technologie doporučujeme používat bezplatnou službu. Mějte na paměti, že počet bezplatných stránek je omezený na 500 za měsíc.

* [Rychlé starty klientské knihovny](./quickstarts/client-library.md) (všechny jazyky, více scénářů)
* Rychlé starty webového uživatelského rozhraní
  * [Výuka pomocí popisků – vzorový Nástroj pro označování](quickstarts/label-tool.md)
* Rychlé starty REST
  * Výuka vlastních modelů a extrahování dat formuláře
    * [Výuka bez popisků – kudrlinkou](quickstarts/curl-train-extract.md)
    * [Výuka bez popisků – Python](quickstarts/python-train-extract.md)
    * [Výuka s popisky – Python](quickstarts/python-labeled-data.md)
  * Extrakce dat z prodejních příjmů
    * [Extrahovat data příjmu – kudrlinkou](quickstarts/curl-receipts.md)
    * [Extrakce dat pro příjem – Python](quickstarts/python-receipts.md)
  * Extrakce dat z vizitek
    * [Extrakce dat obchodních karet – Python](quickstarts/python-business-cards.md)
  * Extrakce textu a struktury tabulky z formulářů
    * [Extrakce dat rozložení – Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>Kontrola rozhraní REST API

Pomocí následujících rozhraní API můžete vyškolit modely a extrahovat strukturovaná data z formulářů.

|Název |Popis |
|---|---|
| **Vlastní model výuky**| Vytvořte nový model pro analýzu formulářů s použitím pěti forem stejného typu. Nastavte parametr _useLabelFile_ na `true` Výukový program s ručně označenými daty. |
| **Analyzovat formulář** |Analyzujte jeden dokument předaný jako datový proud pro extrakci textu, párů klíč/hodnota a tabulek z formuláře pomocí vlastního modelu.  |
| **Analyzovat příjem** |Analyzujte jeden příjmový doklad pro extrakci klíčových informací a dalšího textu příjmu.|
| **Analyzovat obchodní kartu** |Analyzovat vizitku pro extrakci klíčových informací a textu|
| **Analyzovat rozložení** |Umožňuje analyzovat rozložení formuláře pro extrakci textu a struktury tabulky.|

Další informace najdete v [referenční dokumentaci k REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu pro rozpoznávání formulářů, znát zásady Microsoftu u zákaznických dat. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start knihovny klienta](quickstarts/client-library.md) , abyste mohli začít psát aplikaci zpracovávající formuláře pomocí nástroje pro rozpoznávání formulářů v jazyce podle vašeho výběru.