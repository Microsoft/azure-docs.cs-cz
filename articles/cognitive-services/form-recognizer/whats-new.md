---
title: Co je nového ve službě Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Pochopení nejnovějších změn rozhraní API pro rozpoznávání formulářů
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: bbf098feccaf760718b97b1afb2d24bc55eef9ad
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027031"
---
# <a name="whats-new-in-form-recognizer"></a>Co je nového ve službě Rozpoznávání formulářů?

Služba rozpoznávání formulářů se aktualizuje průběžně. Tento článek vám umožní udržovat aktuální informace o vylepšeních, opravách a dokumentaci k funkcím.

> [!NOTE]
> Rychlé starty a příručky pro rozpoznávání formulářů vždy používají nejnovější verzi rozhraní API, pokud nejsou zadány.

## <a name="july-2020"></a>Červenec 2020

### <a name="new-features"></a>Nové funkce
* **dostupný odkaz v 2.0** Podívejte se [na reference k rozhraní API v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) a aktualizované sady SDK pro [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer), [Python](https://docs.microsoft.com/python/api/overview/azure/formrecognizer), [Java](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)a [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer).
* Vylepšení **tabulek a vylepšení extrakce** zahrnují vylepšení přesnosti a vylepšení extrahování tabulek, konkrétně možnost učit se záhlaví a struktury tabulek ve vlastním vlaku bez popisků. 
* **Podpora měn** Detekce a extrakce globálních symbolů měny.
* **Gov Azure** Nástroj pro rozpoznávání formulářů je teď také dostupný v Azure gov.
*   **Rozšířené funkce zabezpečení**: 
    *   **Přineste si vlastní klíč**  Nástroj pro rozpoznávání formulářů automaticky šifruje vaše data při trvalém ukládání do cloudu, aby bylo možné je chránit a aby vám pomohla splnit vaše závazky na zabezpečení a dodržování předpisů v organizaci. Ve výchozím nastavení používá vaše předplatné šifrovací klíče spravované Microsoftem. Vaše předplatné teď můžete spravovat i s vlastními šifrovacími klíči. [Klíče spravované zákazníkem (CMK), označované také jako Přineste si vlastní klíč (BYOK)](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest
), nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.  
    *   **Soukromé koncové body** – umožňují vám používat virtuální síť (VNET) pro [zabezpečený přístup k datům prostřednictvím privátního propojení.](https://docs.microsoft.com/azure/private-link/private-link-overview)


## <a name="june-2020"></a>Červen 2020

### <a name="new-features"></a>Nové funkce
* **Rozhraní API CopyModel přidané do klientských sad SDK** Nyní můžete použít klientské sady SDK ke kopírování modelů z jednoho předplatného do jiného. Obecné informace o této funkci najdete v tématu [zálohování a obnovení modelů](./disaster-recovery.md) .
* **Integrace Azure Active Directory** Nyní můžete použít svoje přihlašovací údaje AAD k ověření objektů klienta pro rozpoznávání ve formuláři v sadách SDK.
* **Změny specifické pro sadu SDK** Zahrnuje to i drobné dodatečné funkce a zásadní změny. Další informace najdete v tématu Protokol změn sady SDK.
  * [Protokol změn sady C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Protokol změn sady Python SDK verze 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 – protokol změn](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 – protokol změn](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Duben 2020

### <a name="new-features"></a>Nové funkce
* **Podpora sady SDK pro rozhraní API pro rozpoznávání formulářů v 2.0 Public Preview** Tento měsíc jsme rozšířili podporu naší služby tak, aby zahrnovala sadu SDK verze Preview pro vydaný formulář pro rozpoznávání formulářů v 2.0 (Preview). Pomocí odkazů níže můžete začít s jazykem, který si vyberete: 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  Nová sada SDK podporuje všechny funkce REST API v 2.0 pro rozpoznávání formulářů. Můžete například vytvořit model s nebo bez popisků a extrahovat text, páry klíčových hodnot a tabulky z formulářů, extrahovat data z účtenek pomocí předem připravené služby příjem a extrahovat text a tabulky pomocí služby rozvržení z vašich dokumentů. Svůj názor na sady SDK můžete sdílet prostřednictvím [formuláře zpětné vazby SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Kopírovat vlastní model** Nyní můžete kopírovat modely mezi oblastmi a odběry pomocí funkce nový vlastní model kopírování. Před vyvoláním rozhraní API pro kopírování vlastního modelu je nutné nejprve získat autorizaci pro kopírování do cílového prostředku voláním operace kopírování do cílového koncového bodu prostředku.
   * [Vygenerovat autorizaci kopírování](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
   * [Zkopírování vlastního modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Vylepšení zabezpečení

* Pro FormRecognizer jsou teď k dispozici spravované klíče zákazníka. Další informace najdete v tématu [šifrování dat v klidovém formátu pro rozpoznávání formulářů](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Pomocí spravovaných identit můžete získat přístup k prostředkům Azure pomocí Azure Active Directory. Další informace najdete v tématu [autorizace přístupu ke spravovaným identitám](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Březen 2020 

### <a name="new-features"></a>Nové funkce

* **Typy hodnot pro popisky** Nyní můžete určit typy hodnot, na které přiřadíte, pomocí nástroje pro rozpoznávání popisků ve formě. V současné době jsou podporovány následující typy hodnot a variace:
  * `string`
    * výchozí, `no-whitespaces` ,`alphanumeric`
  * `number`
    * výchozí`currency`
  * `date` 
    * výchozí, `dmy` , `mdy` ,`ymd`
  * `time`
  * `integer`

  Informace o tom, jak používat tuto funkci, najdete v průvodci [ukázkami popisků](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Vizualizace tabulky** Nástroj pro vzorkování popisků teď zobrazuje tabulky, které byly rozpoznané v dokumentu. To vám umožní zobrazit tabulky, které byly rozpoznány a extrahovány z dokumentu před popisky a analýzou. Tuto funkci můžete zapnout nebo vypnout pomocí možnosti vrstvy.

  Toto je příklad, jak jsou tabulky rozpoznány a extrahovány:

  > [!div class="mx-imgBorder"]
  > ![Vizualizace tabulky pomocí nástroje Sample labeling](./media/whats-new/formre-table-viz.png)

    Extrahované tabulky jsou k dispozici ve výstupu JSON pod `"pageResults"` .

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

Dokončete [rychlý Start](quickstarts/curl-train-extract.md) , abyste mohli začít s [rozhraními API pro rozpoznávání formulářů](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).