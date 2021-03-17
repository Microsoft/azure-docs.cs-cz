---
title: Co je služba Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Služba rozpoznávání formulářů Azure umožňuje identifikovat a extrahovat páry klíč/hodnota a tabulková data z dokumentů formuláře a také extrahovat hlavní informace z prodejních příjmů a vizitek.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: automatizované zpracování dat, zpracování dokumentů, automatizované zadávání dat, zpracování formulářů
ms.openlocfilehash: fdd482a6b0d6ca53d99cd17076ccd9a3545f7879
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467281"
---
# <a name="what-is-form-recognizer"></a>Co je služba Rozpoznávání formulářů?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Nástroj pro rozpoznávání formulářů Azure je softwarová služba, která umožňuje sestavovat automatizovaný software pro zpracování dat pomocí technologie strojového učení. Identifikujte a extrahujte páry text, klíč/hodnota, značky výběru, tabulky a strukturu z dokumentů. služba zapisuje &mdash; strukturovaná data, která zahrnují relace v původním souboru, ohraničující pole, spolehlivost a další. Rychle získáte přesné výsledky, které jsou přizpůsobené vašemu konkrétnímu obsahu bez nutnosti ručních zásahů nebo rozsáhlých znalostí z oblasti datové vědy. Pomocí nástroje pro rozpoznávání formulářů můžete automatizovat zadávání dat ve vašich aplikacích a rozšířit možnosti hledání dokumentů.

Nástroj pro rozpoznávání formulářů se skládá z vlastních modelů zpracování dokumentů, předem vytvořených modelů pro faktury, příjmy, ID a obchodní karty a model rozložení. Můžete volat modely pro rozpoznávání formulářů pomocí REST API nebo sad SDK klientské knihovny, abyste snížili složitost a mohli je integrovat do pracovního postupu nebo aplikace.

Nástroj pro rozpoznávání formulářů se skládá z následujících služeb:

* **[Rozhraní API pro rozložení](#layout-api)** – extrakce textu, značek výběru a struktur tabulek spolu s jejich souřadnicemi ohraničovacích rámečků z dokumentů
* **[Vlastní modely](#custom-models)** – z formulářů extrahuje text, páry klíč/hodnota, značky výběru a tabulková data. Tyto modely jsou vyškolené s vašimi vlastními daty, takže jsou přizpůsobené vašim formám.

* Předem **[připravené modely](#prebuilt-models)** – extrakci dat z jedinečných typů dokumentů pomocí předem vytvořených modelů. Aktuálně dostupné jsou tyto předem připravené modely.

  * [Faktury](./concept-invoices.md)
  * [Prodejní příjmy](./concept-receipts.md)
  * [Vizitky](./concept-business-cards.md)
  * [Karty identifikace (ID)](./concept-identification-cards.md)

## <a name="try-it-out"></a>Vyzkoušet

Chcete-li vyzkoušet službu pro rozpoznávání formulářů, použijte nástroj uživatelské rozhraní online Sample:
<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Vyzkoušet Nástroj pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Vyzkoušet Nástroj pro rozpoznávání formulářů](https://fott.azurewebsites.net/)

---

K vyzkoušení služby pro rozpoznávání formulářů budete potřebovat předplatné Azure ([můžete ho vytvořit zdarma](https://azure.microsoft.com/free/cognitive-services)) a koncový bod a klíč [prostředku pro rozpoznávání formuláře](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) .

## <a name="layout-api"></a>Rozhraní API pro rozložení

Nástroj pro rozpoznávání formulářů může extrahovat text, značky výběru a strukturu tabulky (čísla řádků a sloupců přidružených k tomuto textu) pomocí optického rozpoznávání znaků (OCR) a rozšířeného modelu hloubkového učení z dokumentů. Další informace najdete v koncepční příručce pro [rozvržení](./concept-layout.md) .

:::image type="content" source="./media/tables-example.jpg" alt-text="Příklad tabulek" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Vlastní modely

Vlastní modely pro rozpoznávání formulářů se spouštějí na vaše vlastní data a stačí, abyste mohli začít jenom pět vzorových vstupních formulářů. Model zpracování dokumentů s příškolením může vyvýstupovat strukturovaná data, která obsahují relace v původním dokumentu formuláře. Po proškolování modelu můžete otestovat a znovu ho využít a nakonec ho použít k spolehlivé extrakci dat z dalších formulářů podle vašich potřeb.

Při výuce vlastních modelů máte k dispozici následující možnosti: školení s popisky dat a bez označení dat.

### <a name="train-without-labels"></a>Výuka bez popisků

Nástroj pro rozpoznávání formulářů používá k pochopení rozložení a záznamů mezi poli a položkami ve formulářích bezdohledný Learning. Když odešlete vstupní formuláře, algoritmus clusteruje formuláře podle typu, zjistí, jaké klíče a tabulky jsou k dispozici, a přidruží hodnoty k klíčům a záznamům k tabulkám. Školení bez popisků nevyžaduje ruční označování dat nebo psaní kódu a údržby a doporučujeme tuto metodu vyzkoušet jako první.

Tipy k shromažďování školicích dokumentů najdete v tématu [Vytvoření školicích dat sady](./build-training-data-set.md) .

### <a name="train-with-labels"></a>Výuka s popisky

Když vytváříte výuku s popisky dat, používá model pod dohledem učení k extrakci hodnot, které vás zajímají, pomocí označených formulářů, které zadáte. Výsledkem popisků dat je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.

Nástroj pro rozpoznávání formulářů používá [rozhraní API pro rozložení](#layout-api) a zjišťuje očekávané velikosti a pozice tištěných a ručně psaných textových prvků a extrahování tabulek. Pak pomocí uživatelem zadaných popisků zjistí přidružení klíč/hodnota a tabulky v dokumentech. Doporučujeme použít pět ručně popsaných forem stejného typu (stejné struktury), abyste mohli začít při výuce nového modelu a přidávat další označená data, aby se zlepšila přesnost modelu. Nástroj pro rozpoznávání formulářů umožňuje školicí model pro extrakci párů klíčových hodnot a tabulek pomocí možností učení pod dohledem. 

[Začínáme s visačkami pomocí výukového programu](./quickstarts/label-tool.md)

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prebuilt-models"></a>Předem připravené modely

Nástroj pro rozpoznávání formulářů obsahuje také předem připravené modely pro automatizované zpracování dat jedinečných typů formulářů.

### <a name="prebuilt-invoice-model"></a>Předem sestavený model faktury

Model předem sestavené faktury extrahuje data z faktur v různých formátech a vrátí strukturovaná data. Tento model extrahuje klíčové informace, jako je ID faktury, podrobnosti o zákaznících, podrobnosti o dodavatelích, odeslání do, fakturaci, celková hodnota, daně, Mezisoučet, položky řádků a další. Předem sestavený model faktury je navíc vyškolen k analýze a vrácení veškerého textu a tabulek na faktuře. Další informace najdete v koncepční příručce [faktury](./concept-invoices.md) .

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Ukázková faktura" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Předem sestavený model příjmu

Předem sestavený model příjmu se používá pro čtení prodejních příjmů z Austrálie, Kanady, České Británie, Indie a USA &mdash; typu používaného v restauracích, čerpacích stanicích, maloobchodním prodeji a tak dále. Tento model extrahuje klíčové informace, jako je čas a datum transakce, informace o obchodníkech, množství daní, položek na řádku, součty a další. Předem sestavený model příjmu je navíc vyškolen k analýze a vrácení veškerého textu na účtence. Další informace najdete v koncepční příručce pro [příjem](./concept-receipts.md) .

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Ukázka účtenky" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-identification-id-cards-model"></a>Model předem sestavených karet identifikace (ID)

Model karet identifikace (ID) umožňuje extrahovat klíčové informace z celosvětových cestovních pasů a licencí ovladačů USA. Extrahuje data, jako je ID dokumentu, datum vypršení platnosti narození, datum vypršení platnosti, název, země, oblast, strojově čitelné zóny a další. Další informace najdete v koncepční příručce k [identifikaci (ID) karet](./concept-identification-cards.md) .

:::image type="content" source="./media/overview-id.jpg" alt-text="Ukázka identifikační karty" lightbox="./media/overview-id.jpg":::

### <a name="prebuilt-business-cards-model"></a>Model předdefinovaných vizitek

Model vizitek vám umožňuje extrahovat v angličtině informace, jako je jméno osoby, název úlohy, adresa, e-mail, společnost a telefonní číslo z obchodních karet. Další informace najdete v koncepční příručce pro [obchodní karty](./concept-business-cards.md) .

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Ukázka vizitky" lightbox="./media/overview-business-card.jpg":::

## <a name="get-started"></a>Začínáme

Pomocí nástroje pro rozpoznávání ukázkových formulářů můžete vyzkoušet rozložení, předem připravené modely a vytvořit vlastní model pro vaše dokumenty:  

### <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Vyzkoušet Nástroj pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Vyzkoušet Nástroj pro rozpoznávání formulářů](https://fott.azurewebsites.net/)

---
Pokud chcete začít s extrakcí dat z vašich dokumentů, postupujte podle pokynů v části [Klientská knihovna/REST API Start](./quickstarts/client-library.md) . Při učení technologie doporučujeme používat bezplatnou službu. Mějte na paměti, že počet bezplatných stránek je omezený na 500 za měsíc.

Můžete také začít pomocí ukázek REST (GitHub) – 

* Extrakce textu, značek výběru a struktury tabulek z dokumentů
  * [Extrakce dat rozložení – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-layout.md)
* Výuka vlastních modelů a extrahování dat formuláře
  * [Výuka bez popisků – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md)
  * [Výuka s popisky – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)
* Extrakce dat z faktur
  * [Extrakce dat faktury – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-invoices.md)
* Extrakce dat z prodejních příjmů
  * [Extrakce dat pro příjem – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-receipts.md)
* Extrakce dat z vizitek
  * [Extrakce dat obchodních karet – Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-business-cards.md)

### <a name="review-the-rest-apis"></a>Kontrola rozhraní REST API

Pomocí následujících rozhraní API můžete vyškolit modely a extrahovat strukturovaná data z formulářů.

|Název |Popis |
|---|---|
| **Analyzovat rozložení** | Analyzovat dokument předaný jako datový proud pro extrakci textu, značek výběru, tabulek a struktury z dokumentu |
| **Vlastní model výuky**| Vytvořte nový model pro analýzu formulářů s použitím pěti forem stejného typu. Nastavte parametr _useLabelFile_ na `true` Výukový program s ručně označenými daty. |
| **Analyzovat formulář** |Analyzujte formulář předaný jako datový proud pro extrakci textu, párů klíč/hodnota a tabulek z formuláře pomocí vlastního modelu.  |
| **Analýza faktury** | Analyzuje fakturu pro extrakci klíčových informací, tabulek a textu další faktury.|
| **Analyzovat příjem** | Analyzujte příjmový dokument pro extrakci informací o klíči a další text účtenky.|
| **Analyzovat ID** | Analyzovat dokument karty ID pro extrakci informací o klíči a další text identifikační karty.|
| **Analyzovat obchodní kartu** | Analyzovat vizitku pro extrakci klíčových informací a textu|

### <a name="v21-preview"></a>[verze 2.1 Preview](#tab/v2-1)

Další informace najdete v [referenční dokumentaci k REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

### <a name="v20"></a>[v2.0](#tab/v2-0)

Další informace najdete v [referenční dokumentaci k REST API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm) . Pokud jste obeznámeni s předchozí verzí rozhraní API, přečtěte si článek [co je nového](./whats-new.md) , kde se dozvíte o nejnovějších změnách.

---

## <a name="input-requirements"></a>Požadavky na vstup

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Nasazení místně pomocí kontejnerů Docker

[Použijte kontejnery pro rozpoznávání formulářů (Preview)](form-recognizer-container-howto.md) k nasazení funkcí rozhraní API místně. Tento kontejner Docker vám umožní přiblížit službu k vašim datům z hlediska dodržování předpisů, zabezpečení nebo jiných provozních důvodů.

## <a name="service-availability-and-redundancy"></a>Dostupnost služby a redundance

### <a name="is-form-recognizer-service-zone-resilient"></a>Je služba rozpoznávání formulářů odolná proti chybám?

Ano. Služba rozpoznávání formulářů je ve výchozím nastavení odolná proti zónám.

### <a name="how-do-i-configure-the-form-recognizer-service-to-be-zone-resilient"></a>Návody nakonfigurovat službu rozpoznávání formulářů jako odolnou proti zóně?

Pro povolení odolnosti zóny není nutná žádná konfigurace zákazníka. Zóna – odolnost pro prostředky pro rozpoznávání formulářů je ve výchozím nastavení dostupná a spravovaná samotnou službou.

## <a name="data-privacy-and-security"></a>Ochrana osobních údajů a zabezpečení dat

Stejně jako u všech služeb rozpoznávání by měli vývojáři, kteří používají službu pro rozpoznávání formulářů, znát zásady Microsoftu u zákaznických dat. Další informace najdete na [stránce Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) v centru zabezpečení Microsoftu.

## <a name="next-steps"></a>Další kroky

Vyzkoušejte si náš online nástroj a rychlý Start, kde se dozvíte další informace o službě rozpoznávání formulářů.

* [**Nástroj pro rozpoznávání formulářů**](https://fott-preview.microsoft.com/)
* [**Klientská knihovna a REST API – rychlý Start**](quickstarts/client-library.md)
