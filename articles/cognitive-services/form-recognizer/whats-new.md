---
title: Co je nového ve službě Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Seznamte se s nejnovějšími změnami rozhraní API pro rozpoznávání formulářů.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155167"
---
# <a name="whats-new-in-form-recognizer"></a>Co je nového ve službě Rozpoznávání formulářů?

Služba rozpoznávání formulářů je průběžně aktualizována. V tomto článku můžete mít přehled o vylepšeních funkcí, opravách a aktualizacích dokumentace.

> [!NOTE]
> Rychlé starty a vodítka pro rozpoznávání formulářů vždy používají nejnovější verzi rozhraní API, pokud není zadáno.

## <a name="march-2020"></a>Březen 2020 

### <a name="extraction-enhancements"></a>Vylepšení extrakce

Tato verze obsahuje vylepšení extrakce a vylepšení přesnosti, konkrétně schopnost označovat a extrahovat více párů klíč/hodnota ve stejném řádku textu. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Nástroj pro rozpoznávání vzorků formuláře je nyní open-source

Nástroj pro rozpoznávání vzorků formuláře je nyní k dispozici jako projekt s otevřeným zdrojovým kódem. Můžete jej integrovat do svých řešení a provést změny specifické pro zákazníka, aby vyhovoval vašim potřebám.

Další informace o nástroji pro ukázkové popisky nástroje pro rozpoznávání formulářů naleznete v dokumentaci dostupné na [GitHubu](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Označení typů hodnot

Typy hodnot jsou nyní k dispozici pro použití s nástrojem pro rozpoznávání vzorků formuláře. Tyto typy hodnot jsou aktuálně podporovány: 

* Řetězec
* Číslo 
* Integer
* Datum 
* Time

Tento obrázek znázorňuje, jak vypadá výběr typu hodnoty v nástroji pro vzorové popisování nástroje pro rozpoznávání vzorků formuláře:

> [!div class="mx-imgBorder"]
> ![Výběr typu hodnoty nástrojem pro popisek vzorku](./media/whats-new/formre-value-type.png)

Extrahovaná tabulka je k dispozici ve `pageResults`výstupu JSON v .

### <a name="table-visualization"></a>Vizualizace tabulky 

Nástroj pro rozpoznávání formulářů nyní zobrazuje tabulky, které byly rozpoznány v dokumentu. To umožňuje zobrazit tabulky, které byly rozpoznány a extrahovány z dokumentu, před popisem a analýzou pomocí nástroje pro rozpoznávání vzorků formuláře. Tuto funkci lze zapnout/vypnout pomocí volby vrstev. 

Toto je příklad rozpoznaných a extrahovaných tabulek:

> [!div class="mx-imgBorder"]
> ![Vizualizace tabulky pomocí nástroje pro popisek vzorku](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> Tabulky popisků nejsou podporovány. Pokud tabulky nejsou rozpoznány a extrahovány automaticky, můžete je označit pouze jako páry klíč/hodnota. Při označování tabulek jako párů klíč/hodnota označte každou buňku jako hodnotu.

### <a name="tls-12-enforcement"></a>Vynucení protokolu TLS 1.2

* TLS 1.2 je nyní vynucena pro všechny požadavky HTTP pro tuto službu. Další informace najdete v [tématu Azure Cognitive Services security](../cognitive-services-security.md).

## <a name="january-2020"></a>Leden 2020

Tato verze představuje nástroj pro rozpoznávání formulářů 2.0 (náhled). V následujících částech najdete další informace o nových funkcích, vylepšeních a změnách. 

### <a name="new-features"></a>Nové funkce

* **Vlastní model**
  * **Vlak s etiketami** Nyní můžete trénovat vlastní model s ručně označenými daty. Výsledkem jsou výkonnější modely a mohou vytvářet modely, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.
  * **Asynchronní rozhraní API** Asynchronní volání rozhraní API můžete použít k trénování a analýze velkých datových sad a souborů.
  * **Podpora souborů TIFF** Nyní můžete trénovat a extrahovat data z dokumentů TIFF.
  * **Vylepšení přesnosti extrakce**

* **Předem sestavený model příjmu**
  * **Částky spropitného** Nyní můžete extrahovat množství tipu a další ručně psané hodnoty.
  * **Extrakce řádkové položky** Hodnoty řádkových položek můžete extrahovat z příjmů.
  * **Hodnoty spolehlivosti** Můžete zobrazit spolehlivost modelu pro každou extrahoťovnou hodnotu.
  * **Vylepšení přesnosti extrakce**

* **Extrakce rozvržení** Nyní můžete použít rozhraní API rozložení k extrahování textových dat a dat tabulky z formulářů.

### <a name="custom-model-api-changes"></a>Změny rozhraní API vlastního modelu

Všechna uživatelská prostředí API pro školení a používání vlastních modelů byla přejmenována a některé synchronní metody jsou nyní asynchronní. Hlavní změny jsou následující:

* Proces trénování modelu je nyní asynchronní. Můžete zahájit školení prostřednictvím volání rozhraní API **/custom/models.** Toto volání vrátí ID operace, které můžete předat do **custom/models/{modelID}** vrátit výsledky školení.
* Extrakce klíče/hodnoty je nyní iniciována voláním rozhraní API **/custom/models/{modelID}/analyze.** Toto volání vrátí ID operace, které můžete předat do **vlastní/modely/{modelID}/analyzeResults/{resultID}** vrátit výsledky extrakce.
* ID operace pro operaci Train se nyní nacházejí v hlavičce **Umístění** odpovědí HTTP, nikoli v hlavičce **Umístění operace.**

### <a name="receipt-api-changes"></a>Změny rozhraní API příjmu

Api pro čtení prodejních příjmů byly přejmenovány.

* Extrakce dat příjmu je nyní iniciována voláním rozhraní API **/prebuilt/receipt/analyze.** Toto volání vrátí ID operace, které můžete předat do **/prebuilt/receipt/analyzeResults/{resultID}** vrátit výsledky extrakce.

### <a name="output-format-changes"></a>Změny výstupního formátu

Odpovědi JSON pro všechny volání rozhraní API mají nové formáty. Některé klíče a hodnoty byly přidány, odebrány nebo přejmenovány. Podívejte se na rychlé starty pro příklady aktuálních formátů JSON.

## <a name="next-steps"></a>Další kroky

Chcete-li začít pracovat s [api nástroje PRO Rozpoznávání formulářů ,](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)proveďte rychlý [start.](quickstarts/curl-train-extract.md)