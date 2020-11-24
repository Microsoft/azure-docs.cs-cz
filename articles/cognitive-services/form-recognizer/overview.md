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
ms.openlocfilehash: 8b790da9eaa17cbb0fbdcbcc49682786deb319a0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95492133"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nástroj pro rozpoznávání formulářů Azure je softwarová služba, která umožňuje sestavovat automatizovaný software pro zpracování dat pomocí technologie strojového učení. Identifikujte a extrahujte páry text, klíč/hodnota, značky výběru, tabulky a strukturu z dokumentů. služba zapisuje &mdash; strukturovaná data, která zahrnují relace v původním souboru, ohraničující pole, spolehlivost a další. Rychle získáte přesné výsledky, které jsou přizpůsobené vašemu konkrétnímu obsahu bez nutnosti ručních zásahů nebo rozsáhlých znalostí z oblasti datové vědy. Pomocí nástroje pro rozpoznávání formulářů můžete automatizovat zadávání dat ve vašich aplikacích a rozšířit možnosti hledání dokumentů.

Nástroj pro rozpoznávání formulářů se skládá z vlastních modelů zpracování dokumentů, předem vytvořených modelů pro faktury, příjmy a obchodní karty a model rozložení. Můžete volat modely pro rozpoznávání formulářů pomocí REST API nebo sad SDK klientské knihovny, abyste snížili složitost a mohli je integrovat do pracovního postupu nebo aplikace.

Nástroj pro rozpoznávání formulářů se skládá z následujících služeb:
* **[Rozhraní API pro rozložení](#layout-api)** – extrakce textu, značek výběru a struktur tabulek spolu s jejich souřadnicemi ohraničovacích rámečků z dokumentů
* **[Vlastní modely](#custom-models)** – z formulářů extrahuje text, páry klíč/hodnota, značky výběru a tabulková data. Tyto modely jsou vyškolené s vašimi vlastními daty, takže jsou přizpůsobené vašim formám.
* Předem **[připravené modely](#prebuilt-models)** – extrakce dat z jedinečných typů formulářů pomocí předem sestavených modelů. Aktuálně dostupné jsou tyto předem připravené modely.
    * [Faktury](./concept-invoices.md) 
    * [Prodejní příjmy](./concept-receipts.md)
    * [Vizitky](./concept-business-cards.md) 


## <a name="try-it-out"></a>Vyzkoušet

Chcete-li vyzkoušet službu pro rozpoznávání formulářů, použijte nástroj uživatelské rozhraní online Sample:


# <a name="v20"></a>[v2.0](#tab/v2-0)
> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)
> [!div class="nextstepaction"]
> [Vyzkoušet předem připravené modely](https://fott-preview.azurewebsites.net/)

---

K vyzkoušení služby pro rozpoznávání formulářů budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a koncový bod a klíč [prostředku pro rozpoznávání formuláře](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) . 

## <a name="layout-api"></a>Rozhraní API pro rozložení

Nástroj pro rozpoznávání formulářů může extrahovat text, značky výběru a strukturu tabulky (čísla řádků a sloupců přidružených k tomuto textu) pomocí optického rozpoznávání znaků (OCR) a rozšířeného modelu hloubkového učení z dokumentů. Další informace najdete v koncepční příručce pro [rozvržení](./concept-layout.md) .

:::image type="content" source="./media/tables-example.jpg" alt-text="Příklad tabulek" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Vlastní modely

Vlastní modely pro rozpoznávání formulářů se spouštějí na vaše vlastní data a stačí, abyste mohli začít jenom pět vzorových vstupních formulářů. Model zpracování dokumentů s příškolením může vyvýstupovat strukturovaná data, která obsahují relace v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

Při výuce vlastních modelů máte k dispozici následující možnosti: školení s popisky dat a bez označení dat.

### <a name="train-without-labels"></a>Výuka bez popisků

Ve výchozím nastavení používá nástroj pro rozpoznávání formulářů nepod dohledem informace o rozložení a vztazích mezi poli a položkami ve formulářích. Když odešlete vstupní formuláře, algoritmus clusteruje formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty k klíčům a záznamům k tabulkám. To nevyžaduje ruční označování dat nebo psaní kódu a údržby a doporučujeme tuto metodu vyzkoušet jako první.

Tipy k shromažďování školicích dokumentů najdete v tématu [Vytvoření školicích dat sady](./build-training-data-set.md) .

### <a name="train-with-labels"></a>Výuka s popisky

Při výuce s povzorovými daty model předává pod dohledem učení o extrakci hodnot, které vás zajímají, pomocí popisků, které zadáte. Výsledkem je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.

Nástroj pro rozpoznávání formulářů používá [rozhraní API pro rozložení](#layout-api) a zjišťuje očekávané velikosti a pozice vytištěných a ručně psaných textových prvků. Pak pomocí uživatelem zadaných popisků zjistí přidružení klíč/hodnota v dokumentech. Doporučujeme použít pět ručně popsaných forem stejného typu (stejné struktury), abyste mohli začít při výuce nového modelu a přidávat další označená data, aby se zlepšila přesnost modelu.

[Začínáme s visačkami pomocí výukového programu](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Předem připravené modely

Nástroj pro rozpoznávání formulářů obsahuje také předem připravené modely pro automatizované zpracování dat jedinečných typů formulářů.

### <a name="prebuilt-invoice-model"></a>Předem sestavený model faktury
Model předem sestavené faktury extrahuje data z faktur v nejrůznějších formátech a vrátí strukturovaná data. Tento model extrahuje klíčové informace, jako je ID faktury, podrobnosti o zákaznících, podrobnosti o dodavateli, odeslání do, fakturaci, celková hodnota, daň, Mezisoučet a další. Předem sestavený model faktury je navíc vyškolen pro rozpoznání a vrácení veškerého textu a tabulek na faktuře. Další informace najdete v koncepční příručce [faktury](./concept-invoices.md) .

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Ukázková faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Předem sestavený model příjmu se používá pro čtení prodejních příjmů z Austrálie, Kanady, České Británie, Indie a USA &mdash; typu používaného v restauracích, čerpacích stanicích, maloobchodním prodeji a tak dále. Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkech, množství daní, položek na řádku, součty a další. Předem sestavený model příjmu je navíc vyškolen pro rozpoznání a vrácení veškerého textu na účtence. Další informace najdete v koncepční příručce pro [příjem](./concept-receipts.md) .

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Ukázka účtenky" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Model předdefinovaných vizitek

Model vizitek vám umožňuje extrahovat v angličtině informace, jako je jméno osoby, název úlohy, adresa, e-mail, společnost a telefonní číslo z obchodních karet. Další informace najdete v koncepční příručce pro [obchodní karty](./concept-business-cards.md) .

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Ukázka vizitky" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Začínáme

Pomocí [Nástroje pro rozpoznávání ukázkového formuláře](https://fott.azurewebsites.net/) nebo pomocí rychlého startu začněte s extrakcí dat z vašich formulářů. Při učení technologie doporučujeme používat bezplatnou službu. Mějte na paměti, že počet bezplatných stránek je omezený na 500 za měsíc.

* [Rychlé starty klientské knihovny](./quickstarts/client-library.md) (všechny jazyky, více scénářů)
* Rychlé starty webového uživatelského rozhraní
  * [Výuka pomocí popisků – vzorový Nástroj pro označování](quickstarts/label-tool.md)
* Rychlé starty REST
 * Extrakce textu, značek výběru a struktury tabulek z dokumentů
    * [Extrakce dat rozložení – Python](quickstarts/python-layout.md)
  * Výuka vlastních modelů a extrahování dat formuláře
    * [Výuka bez popisků – kudrlinkou](quickstarts/curl-train-extract.md)
    * [Výuka bez popisků – Python](quickstarts/python-train-extract.md)
    * [Výuka s popisky – Python](quickstarts/python-labeled-data.md)
  * Extrakce dat z faktur
    * [Extrakce dat faktury – Python](quickstarts/python-invoices.md)
  * Extrakce dat z prodejních příjmů
    * [Extrahovat data příjmu – kudrlinkou](quickstarts/curl-receipts.md)
    * [Extrakce dat pro příjem – Python](quickstarts/python-receipts.md)
  * Extrakce dat z vizitek
    * [Extrakce dat obchodních karet – Python](quickstarts/python-business-cards.md)
 


### <a name="review-the-rest-apis"></a>Kontrola rozhraní REST API

Pomocí následujících rozhraní API můžete vyškolit modely a extrahovat strukturovaná data z formulářů.

|Název |Popis |
|---|---|
| **Analyzovat rozložení** | Analyzovat dokument předaný jako datový proud pro extrakci textu, značek výběru, tabulek a struktur z dokumentu |
| **Vlastní model výuky**| Vytvořte nový model pro analýzu formulářů s použitím pěti forem stejného typu. Nastavte parametr _useLabelFile_ na `true` Výukový program s ručně označenými daty. |
| **Analyzovat formulář** |Analyzujte formulář předaný jako datový proud pro extrakci textu, párů klíč/hodnota a tabulek z formuláře pomocí vlastního modelu.  |
| **Analýza faktury** | Analyzuje fakturu pro extrakci klíčových informací, tabulek a textu další faktury.|
| **Analyzovat příjem** | Analyzujte příjmový dokument pro extrakci informací o klíči a další text účtenky.|
| **Analyzovat obchodní kartu** | Analyzovat vizitku pro extrakci klíčových informací a textu|


# <a name="v20"></a>[v2.0](#tab/v2-0)
Další informace najdete v [referenční dokumentaci k REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

# <a name="v21"></a>[v 2.1](#tab/v2-1)
Další informace najdete v [referenční dokumentaci k REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

---

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu pro rozpoznávání formulářů, znát zásady Microsoftu u zákaznických dat. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start knihovny klienta](quickstarts/client-library.md) , abyste mohli začít psát aplikaci zpracovávající formuláře pomocí nástroje pro rozpoznávání formulářů v jazyce podle vašeho výběru.
