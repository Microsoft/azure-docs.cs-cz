---
title: Co je nového ve službě Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Pochopení nejnovějších změn rozhraní API pro rozpoznávání formulářů
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531078"
---
# <a name="whats-new-in-form-recognizer"></a>Co je nového ve službě Rozpoznávání formulářů?

Služba rozpoznávání formulářů se aktualizuje průběžně. Tento článek vám umožní udržovat aktuální informace o vylepšeních, opravách a dokumentaci k funkcím.

> [!NOTE]
> Rychlé starty a příručky pro rozpoznávání formulářů vždy používají nejnovější verzi rozhraní API, pokud nejsou zadány.

## <a name="march-2020"></a>Březen 2020 

### <a name="new-features"></a>Nové funkce

* **Typy hodnot pro popisky** Nyní můžete určit typy hodnot, na které přiřadíte, pomocí nástroje pro rozpoznávání popisků ve formě. V současné době jsou podporovány následující typy hodnot a variace:
  * `string`
    * výchozí, `no-whitespaces`,`alphanumeric`
  * `number`
    * výchozí`currency`
  * `date` 
    * výchozí, `dmy`, `mdy`,`ymd`
  * `time`
  * `integer`

  Informace o tom, jak používat tuto funkci, najdete v průvodci [ukázkami popisků](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Vizualizace tabulky** Nástroj pro vzorkování popisků teď zobrazuje tabulky, které byly rozpoznané v dokumentu. To vám umožní zobrazit tabulky, které byly rozpoznány a extrahovány z dokumentu před popisky a analýzou. Tuto funkci můžete zapnout nebo vypnout pomocí možnosti vrstvy.

  Toto je příklad, jak jsou tabulky rozpoznány a extrahovány:

  > [!div class="mx-imgBorder"]
  > ![Vizualizace tabulky pomocí nástroje Sample labeling](./media/whats-new/formre-table-viz.png)

    Extrahované tabulky jsou k dispozici ve výstupu JSON `"pageResults"`pod.

  > [!IMPORTANT]
  > Tabulky popisků se nepodporují. Pokud se tabulky nerozpoznají a extrated automaticky, můžete je označit jenom jako páry klíč/hodnota. Při označování tabulek jako párů klíč/hodnota označte každou buňku jako jedinečnou hodnotu.

### <a name="extraction-enhancements"></a>Vylepšení extrakce

Tato verze zahrnuje vylepšení a vylepšení přesnosti extrakce, konkrétně možnost označovat a extrahovat více párů klíč/hodnota na stejném řádku textu. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Ukázka labeling Tool je teď Open-Source.

Nástroj pro vyznačení ukázky pro rozpoznávání formulářů je teď dostupný jako open source projekt. Můžete ji integrovat v rámci svých řešení a provádět změny specifické pro konkrétní zákazníky, které odpovídají vašim potřebám.

Další informace o nástroji pro označování ukázek na základě rozpoznávání formulářů najdete v dokumentaci k dispozici na [GitHubu](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Vynucení protokolu TLS 1.2

Pro všechny požadavky HTTP na tuto službu se teď vynutilo TLS 1,2. Další informace najdete v tématu [zabezpečení Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Leden 2020

Tato verze zavádí nástroj pro rozpoznávání formulářů 2,0 (Preview). V následujících částech najdete další informace o nových funkcích, vylepšeních a změnách. 

### <a name="new-features"></a>Nové funkce

* **Vlastní model**
  * **Výuka s popisky** Nyní můžete vytvořit vlastní model s ručně označenými daty. Výsledkem je lepší provádění modelů a může způsobit vytváření modelů, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.
  * **Asynchronní rozhraní API** Asynchronní volání rozhraní API můžete použít ke školení a analýze velkých datových sad a souborů.
  * **Podpora souborů TIFF** Nyní můžete data z dokumentů TIFF vyškolit a extrahovat.
  * **Vylepšení přesnosti extrakce**

* **Předem sestavený model příjmu**
  * **Částky hrotu** Nyní můžete extrahovat množství tipů a další ručně psané hodnoty.
  * **Extrakce položky řádku** Můžete extrahovat hodnoty položek řádků z příjmů.
  * **Hodnoty spolehlivosti** U každé extrahované hodnoty můžete zobrazit důvěru modelu.
  * **Vylepšení přesnosti extrakce**

* **Extrakce rozložení** Rozhraní API pro rozložení teď můžete použít k extrakci textových dat a tabulkových dat z vašich formulářů.

### <a name="custom-model-api-changes"></a>Změny rozhraní API vlastního modelu

Všechna rozhraní API pro školení a používání vlastních modelů byla přejmenována a některé synchronní metody jsou nyní asynchronní. Níže jsou uvedené zásadní změny:

* Proces školení modelu je nyní asynchronní. Zahájíte školení prostřednictvím volání rozhraní **/Custom/Models** API. Toto volání vrátí ID operace, kterou můžete předat **vlastním/modelům/{modelID}** a vrátit výsledky školení.
* Extrakce klíč/hodnota je nyní iniciována voláním rozhraní API **/Custom/Models/{modelID}/Analyze** . Toto volání vrátí ID operace, kterou můžete předat **vlastním/modelům/{modelID}/analyzeResults/{hodnotu resultid}** a vrátit výsledky extrakce.
* ID operací pro operaci vlaku se nyní nacházejí v hlavičce **umístění** odpovědí HTTP, nikoli v hlavičce **umístění operace** .

### <a name="receipt-api-changes"></a>Změny rozhraní API pro příjem

Rozhraní API pro čtení prodejních příjmů bylo přejmenováno.

* Extrakce dat pro příjem se teď iniciuje voláním rozhraní API **/prebuilt/Receipt/Analyze** . Toto volání vrátí ID operace, kterou můžete předat do **/prebuilt/Receipt/analyzeResults/{resultID}** a vrátit výsledky extrakce.

### <a name="output-format-changes"></a>Změny formátu výstupu

Odpovědi JSON pro všechna volání rozhraní API mají nové formáty. Některé klíče a hodnoty byly přidány, odebrány nebo přejmenovány. Příklady aktuálních formátů JSON najdete v rychlých startech.

## <a name="next-steps"></a>Další kroky

Dokončete [rychlý Start](quickstarts/curl-train-extract.md) , abyste mohli začít s [rozhraními API pro rozpoznávání formulářů](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).