---
title: Co je nového ve službě Rozpoznávání formulářů?
titleSuffix: Azure Cognitive Services
description: Pochopení nejnovějších změn rozhraní API pro rozpoznávání formulářů
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81115f5a9ed802f1d07c45ec928dc4b84ea2917b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048744"
---
<!-- markdownlint-disable MD024 -->
# <a name="whats-new-in-form-recognizer"></a>Co je nového ve službě Rozpoznávání formulářů?

Služba rozpoznávání formulářů se aktualizuje průběžně. Tento článek vám umožní udržovat aktuální informace o vylepšeních, opravách a dokumentaci k funkcím.

## <a name="march-2021"></a>Březen 2021

**Nástroj pro rozpoznávání formulářů verze 2.1 Public Preview 3 je nyní k dispozici.** verze 2.1-Preview. 3 byla vydaná, včetně následujících funkcí:

- **Nový předem sestavený model ID** Nový předem sestavený model ID umožňuje zákazníkům přijímat ID a vracet strukturovaná data pro automatizaci zpracování. Kombinuje naše výkonné funkce optického rozpoznávání znaků (OCR) s ID porozumění modelům pro extrakci klíčových informací z cestovních pasů a licencí na ovladače USA, jako je jméno, datum narození, datum vydání, datum vypršení platnosti a další.

  [Další informace o předem připraveném modelu ID](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="Příklad služby Passport" lightbox="./media/id-canada-passport-example.png":::

- **Extrakce položek řádků pro předem sestavený model faktury** : předem sestavený model faktury teď podporuje extrakci položek řádků; nyní extrahuje celé položky a jejich části Popis, množství, množství, ID produktu, datum a další. Pomocí jednoduchého volání rozhraní API nebo sady SDK můžete extrahovat užitečná data z vašich faktur – text, tabulka, páry klíč-hodnota a položky řádku.

   [Další informace o předem vytvořeném modelu faktury](concept-invoices.md)

- **Pod dohledem a přípravou a školením v tabulce,** kromě [funkcí pro automatické extrakci](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)formulářů pro rozpoznávání formulářů, je teď možné, že zákazníci můžou označovat tabulky popisků a vlaků. Tato nová verze zahrnuje možnost popisků a vlaků v řádkových položkách/tabulkách (dynamické a pevné) a výuku vlastního modelu pro extrakci párů klíč-hodnota a položek řádků. Po vyškolení modelu model extrahuje položky řádku jako součást výstupu JSON v oddílu documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Popis tabulky" lightbox="./media/table-labeling.png":::

    Kromě popisků tabulek teď vy a popisek prázdné hodnoty a oblasti. Pokud některé dokumenty v sadě školení neobsahují hodnoty pro určitá pole, můžete je použít, aby váš model věděl, že hodnoty budou z analyzovaných dokumentů správně extrahovány.

- **Podpora pro 66 nové jazyky** – rozhraní API pro rozložení a vlastní modely pro rozpoznávání formulářů teď podporuje 73 jazyků.

  [Další informace o podpoře jazyka pro rozpoznávání formulářů](language-support.md)

- **Přirozené pořadí čtení, klasifikace rukopisu a výběr stránky** – pomocí této aktualizace se můžete rozhodnout, že se mají načíst výstupy textových řádků v přirozeném pořadí čtení namísto výchozího řazení zleva doprava a shora dolů. Použijte nový parametr dotazu readingOrder a nastavte jej na "přirozený" hodnotu pro přesnější výstup pořadí čtení. Pro jazyky v latince navíc Nástroj pro rozpoznávání formulářů klasifikuje textové řádky jako ručně psaný styl, nikoli a poskytuje hodnocení spolehlivosti.

- **Předem připravená vylepšení kvality modelu příjmu** Tato aktualizace zahrnuje řadu vylepšení kvality pro předem sestavený model příjmu, zejména kolem extrakce položky řádku.

## <a name="november-2020"></a>Listopad 2020

### <a name="new-features"></a>Nové funkce

**Nástroj pro rozpoznávání formulářů v 2.1 Public Preview 2 je nyní k dispozici.** verze 2.1-Preview. 2 byla vydaná, včetně následujících funkcí: 

- **Nový předem sestavený model faktury** – nový předem sestavený model faktury umožňuje zákazníkům přebírat faktury v nejrůznějších formátech a vracet strukturovaná data pro automatizaci zpracování faktury. Kombinuje naše výkonné funkce optického rozpoznávání znaků (OCR) s fakturací s porozuměním modelem obsáhlého učení pro extrakci klíčových informací z faktur v angličtině. Extrahuje text, tabulky a informace, jako je například zákazník, dodavatel, ID faktury, datum splatnosti faktury, celková částka, splatnost, částka daně, částka pro odeslání, fakturaci a další.

  > [Další informace o předem vytvořeném modelu faktury](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="Příklad faktury" lightbox="./media/invoice-example.jpg":::

- **Vylepšená extrakce tabulky** – Nástroj pro rozpoznávání formulářů teď nabízí rozšířenou extrakci tabulky, která kombinuje naše výkonné funkce optického rozpoznávání znaků (OCR) s modelem extrakce tabulky hloubkového učení. Nástroj pro rozpoznávání formulářů může extrahovat data z tabulek, včetně složitých tabulek se sloučenými sloupci, řádky, bez ohraničení a další. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="Příklad tabulek" lightbox="./media/tables-example.jpg":::

 
  > [Další informace o extrakci rozložení](concept-layout.md)

- **Aktualizace klientské knihovny** – nejnovější verze [klientských knihoven](quickstarts/client-library.md) pro .NET, Python, Java a JavaScript podporují rozhraní API pro rozpoznávání formulářů 2,1.
- **Podporován nový jazyk: japonština** : nyní jsou podporovány následující nové jazyky: pro `AnalyzeLayout` a `AnalyzeCustomForm` : japonština ( `ja` ). [Podpora jazyků](language-support.md)
- **Označení stylu čáry textu (ručně psané) (jenom v jazycích Latin)** – Nástroj pro rozpoznávání formulářů teď vytvoří výstup `appearance` objektu, který klasifikuje, jestli je každý textový řádek v rukopisu, nebo ne, společně s skóre spolehlivosti. Tato funkce je podporovaná jenom pro jazyky v latince.
- **Vylepšení kvality** – vylepšení extrakce, včetně vylepšení pro extrakci s jednou číslicí.
- **Nová funkce try-to-out v nástroji pro rozpoznávání formulářů a nástroj pro popisování** – schopnost vyzkoušet předem připravené modely faktur, příjmů a obchodních karet a rozhraní API rozložení pomocí nástroje pro vzorkování ukázek pro rozpoznávání formulářů. Podívejte se, jak budou data extrahována bez psaní kódu.

  > [Vyzkoušejte si vzorový Nástroj pro rozpoznávání formulářů](https://fott-preview.azurewebsites.net/)

  ![Příklad FOTT](./media/ui-preview.jpg)
  
- **Smyčka zpětné vazby** – při analýze souborů prostřednictvím nástroje pro označování ukázek se teď můžete přidat i do sady školení a v případě potřeby upravit štítky a vytvořit výuku pro zlepšení modelu.
- **Automatické označování dokumentů** – automaticky označí další dokumenty na základě předchozích označených dokumentů v projektu.

## <a name="august-2020"></a>Srpen 2020

### <a name="new-features"></a>Nové funkce

**Verze Public Preview pro rozpoznávání formulářů v 2.1 je nyní k dispozici.** Verze 2.1-Preview. 1 byla uvolněna, včetně následujících funkcí: 


- **Odkaz na REST API je k dispozici** – Podívejte se na [odkaz v 2.1-Preview. 1.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Nové jazyky podporované kromě angličtiny** jsou teď podporované tyto [jazyky](language-support.md) : pro `Layout` a `Train Custom Model` : angličtina ( `en` ), čínština (zjednodušená) ( `zh-Hans` ), holandština ( `nl` ), francouzština (), `fr` němčina (), `de` italština () `it` , portugalština () `pt` a španělština ( `es` ).
- **Detekce značek zaškrtnutí nebo výběru** – Nástroj pro rozpoznávání formulářů podporuje detekci a extrakci značek výběru, jako jsou zaškrtávací políčka a přepínače. Značky výběru jsou extrahovány v `Layout` a nyní můžete také označovat popisky a vlaky v `Train Custom Model`  -  _vlakech pomocí popisků_ pro extrakci párů klíč-hodnota pro značky výběru. 
- **Vytváření modelů** – umožňuje sestavit více modelů a volat je pomocí jediného ID modelu. Při odeslání dokumentu, který má být analyzován pomocí ID složeného modelu, je nejprve proveden krok klasifikace, který bude směrován do správného vlastního modelu. Vytváření modelů je k dispozici pro `Train Custom Model`  -  _vlaky s popisky_.
- **Název modelu** – přidejte do vlastních modelů popisný název pro snazší správu a sledování.
- **[Nový předem sestavený model pro vizitky](concept-business-cards.md)** pro extrakci společných polí v angličtině, jazykových obchodních karet.
- **[Nové národní prostředí pro předem připravené příjmy](concept-receipts.md)** kromě en-US je teď k dispozici pro en-au, en-CA, en-GB, EN-in.
- **Vylepšení kvality** pro `Layout` , `Train Custom Model`  -  _výuku bez popisků_ a _vlaků s popisky_.

**v 2.0** zahrnuje následující aktualizaci:

- [Klientské knihovny](quickstarts/client-library.md) pro NET, Python, Java a JavaScript vstoupily do všeobecné dostupnosti. 

**Nové ukázky** jsou k dispozici na GitHubu. 

- [Recepty pro extrakci znalostní báze – Forms PlayBook](https://github.com/microsoft/knowledge-extraction-recipes-forms) shromažďuje osvědčené postupy z reálných zákaznických zapojení pro rozpoznávání a poskytuje použitelné ukázky kódu, kontrolní seznamy a ukázkové kanály používané při vývoji těchto projektů. 
- [Nástroj pro označování ukázek](https://github.com/microsoft/OCR-Form-Tools) byl aktualizován tak, aby podporoval nové funkce v 2.1. V tomto [rychlém](quickstarts/label-tool.md) startu najdete informace o tom, jak začít s nástrojem. 
- Ukázka rozpoznávání formuláře [inteligentního veřejného terminálu](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) ukazuje, jak integrovat `Analyze Receipt` a `Train Custom Model`  -  _vyškolit bez popisků_.

## <a name="july-2020"></a>Červenec 2020

### <a name="new-features"></a>Nové funkce
<!-- markdownlint-disable MD004 -->
* **dostupné reference v 2.0** – Podívejte se na [Reference k rozhraní API v 2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) a aktualizované sady SDK pro [.NET](/dotnet/api/overview/azure/ai.formrecognizer-readme), [Python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)a [JavaScript](/javascript/api/overview/azure/).
* Vylepšení **tabulek a vylepšení extrakce** – obsahuje vylepšení přesnosti a vylepšení extrahování tabulek, konkrétně možnost učit se záhlaví a struktury tabulek ve _vlastním vlaku bez popisků_. 

* **Podpora měny** – detekce a extrakce globálních symbolů měn.
* **Azure gov** – Nástroj pro rozpoznávání formulářů je teď dostupný i v Azure gov.
* **Rozšířené funkce zabezpečení**: 
  * **Přineste si vlastní** Nástroj pro rozpoznávání vlastního klíče, který vaše data automaticky šifruje, když jsou trvale chráněná v cloudu, a pomůžou vám splnit závazky zabezpečení a dodržování předpisů vaší organizace. Vaše předplatné ve výchozím nastavení používá šifrovací klíče spravované Microsoftem. Vaše předplatné teď můžete spravovat i s vlastními šifrovacími klíči. [Klíče spravované zákazníkem, označované také jako Přineste si vlastní klíč (BYOK)](./encrypt-data-at-rest.md), nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Šifrovací klíče sloužící k ochraně vašich dat můžete také auditovat.  
  * **Soukromé koncové body** – umožňují vám používat virtuální síť (VNET) pro [zabezpečený přístup k datům prostřednictvím privátního propojení.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Červen 2020

### <a name="new-features"></a>Nové funkce

* **Rozhraní API CopyModel bylo přidáno do klientských sad SDK** – nyní můžete použít klientské sady SDK ke kopírování modelů z jednoho předplatného do jiného. Obecné informace o této funkci najdete v tématu [zálohování a obnovení modelů](./disaster-recovery.md) .
* **Azure Active Directory Integration** – nyní můžete použít přihlašovací údaje služby Azure AD k ověřování objektů klienta pro rozpoznávání ve formuláři v sadách SDK.
* **Změny specifické pro sadu SDK** – to zahrnuje i drobné dodatečné funkce a zásadní změny. Další informace najdete v tématu Protokol změn sady SDK.
  * [Protokol změn sady C# SDK Preview 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Protokol změn sady Python SDK verze 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 – protokol změn](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK Preview 3 – protokol změn](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>Duben 2020

### <a name="new-features"></a>Nové funkce

* **Podpora sady SDK pro rozhraní API pro rozpoznávání formulářů v 2.0 Public Preview** – tento měsíc rozšířili jsme podporu naší služby tak, aby ZAHRNOVALA sadu SDK verze Preview pro vydaný formulář pro rozpoznávání formulářů v 2.0 (Preview). Pomocí odkazů níže můžete začít s jazykem, který si vyberete: 
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Nová sada SDK podporuje všechny funkce REST API v 2.0 pro rozpoznávání formulářů. Můžete například vytvořit model s nebo bez popisků a extrahovat text, páry klíčových hodnot a tabulky z formulářů, extrahovat data z účtenek pomocí předem připravené služby příjem a extrahovat text a tabulky pomocí služby rozvržení z vašich dokumentů. Svůj názor na sady SDK můžete sdílet prostřednictvím [formuláře zpětné vazby SDK](https://aka.ms/FR_SDK_v1_feedback).

* **Kopírovat vlastní model** Nyní můžete kopírovat modely mezi oblastmi a odběry pomocí funkce nový vlastní model kopírování. Před vyvoláním rozhraní API pro kopírování vlastního modelu je nutné nejprve získat autorizaci pro kopírování do cílového prostředku voláním operace kopírování do cílového koncového bodu prostředku.

  * [Vygenerovat autorizaci kopírování](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Zkopírování vlastního modelu](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Vylepšení zabezpečení

* Customer-Managed klíče jsou nyní k dispozici pro FormRecognizer. Další informace najdete v tématu [šifrování dat v klidovém formátu pro rozpoznávání formulářů](./encrypt-data-at-rest.md).
* Pomocí spravovaných identit můžete získat přístup k prostředkům Azure pomocí Azure Active Directory. Další informace najdete v tématu [autorizace přístupu ke spravovaným identitám](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Březen 2020

### <a name="new-features"></a>Nové funkce

* **Typy hodnot pro popisky** Nyní můžete určit typy hodnot, na které přiřadíte, pomocí nástroje pro rozpoznávání popisků ve formě. V současné době jsou podporovány následující typy hodnot a variace:
  * `string`
    * výchozí, `no-whitespaces` , `alphanumeric`
  * `number`
    * výchozí `currency`
  * `date` 
    * výchozí, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  Informace o tom, jak používat tuto funkci, najdete v průvodci [ukázkami popisků](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Vizualizace tabulky** Nástroj pro vzorkování popisků teď zobrazuje tabulky, které byly rozpoznané v dokumentu. Tato funkce umožňuje zobrazit tabulky, které byly rozpoznány a extrahovány z dokumentu před popisky a analýzou. Tuto funkci můžete zapnout nebo vypnout pomocí možnosti vrstvy.

  Následující obrázek je příkladem způsobu, jakým jsou tabulky rozpoznány a extrahovány:

  > [!div class="mx-imgBorder"]
  > ![Vizualizace tabulky pomocí nástroje Sample labeling](./media/whats-new/table-viz.png)

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
  * **Výuka s popisky** Nyní můžete vytvořit vlastní model s ručně označenými daty. Tato metoda vede k lepšímu provádění modelů a může vydávat modely, které pracují se složitými formuláři nebo formuláři obsahujícími hodnoty bez klíčů.
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

Dokončete [rychlý Start](quickstarts/client-library.md) , abyste mohli začít psát aplikaci zpracovávající formuláře pomocí nástroje pro rozpoznávání formulářů ve vývojovém jazyce podle vašeho výběru.

## <a name="see-also"></a>Viz také

* [Co je služba Rozpoznávání formulářů?](./overview.md)