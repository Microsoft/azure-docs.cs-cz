---
title: Zdroje dat podporované – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker automaticky extrahuje páry otázka – odpověď z částečně strukturovaných obsah, jako jsou nejčastější dotazy, produktových příruček, pokyny, podporu dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace Word. Obsah lze také přidat do znalostní báze ze strukturovaného QnA obsahu souborů.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: diberry
ms.openlocfilehash: 5175dee24542c716b3d087412864ae7e6f056d18
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615982"
---
# <a name="data-sources-for-qna-maker-content"></a>Zdroje dat pro nástroj QnA Maker obsahu

Nástroj QnA Maker automaticky extrahuje páry otázka – odpověď z částečně strukturovaných obsah, jako jsou nejčastější dotazy, produktových příruček, pokyny, podporu dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace Word. Obsah lze také přidat do znalostní báze ze strukturovaného QnA obsahu souborů. 

## <a name="data-types"></a>Typy dat

Následující tabulka shrnuje typy obsahu a souboru formáty, které podporuje QnA Maker.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|URL|Nejčastější dotazy<br> (Plochý, s oddíly nebo s domovskou stránkou témat)<br>Stránky podpory <br> (Články s návody na jednu stranu, články týkající se řešení potíží atd.)|[Jednoduché Nejčastější dotazy](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Nejčastější dotazy s odkazy](https://www.microsoft.com/software-download/faq)<br> [Nejčastější dotazy s domovskou stránkou s tématy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Článek podpory](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|SOUBOR PDF / DOC|Nejčastější<br> Ruční produkt,<br> Brožury,<br> Formátech<br> Zásady oznámení<br> Průvodce podporou,<br> Strukturované QnA,<br> atd.|[Strukturované QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka ručního produktu. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Ukázka semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka dokumentu White Paper. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Strukturované QnA souboru<br> (včetně formátu RTF, HTML support)|[Ukázka QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturované QnA souboru|[Ukázka chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Umístění zdroje dat.

Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověřování. 

Pokud potřebujete ověření pro zdroj dat, můžete k získání těchto dat QnA Maker použít následující metody:

* [Stáhnout soubor ručně](#download-file-from-authenticated-data-source-location) a importovat do QnA maker
* Importovat soubor pro ověřené [umístění služby SharePoint](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Stáhnout soubor z umístění ověřeného zdroje dat

Pokud máte ověřený soubor (ne v ověřeném umístění služby SharePoint) nebo adresu URL, je alternativním parametrem stažení souboru z ověřené lokality do místního počítače a následné přidání souboru z místního počítače do znalostní báze.

### <a name="import-file-from-authenticated-sharepoint"></a>Importovat soubor z ověřeného SharePointu 

[Umístění zdrojů dat služby SharePoint](../How-to/add-sharepoint-datasources.md) můžou poskytovat ověřené **soubory**. Prostředky SharePoint musí být soubory, nikoli webové stránky. V případě, že adresa URL končí rozšířením web, například **. ASPX**se nebude naimportovat do QnA maker ze SharePointu.


## <a name="faq-urls"></a>Nejčastější dotazy týkající se adresy URL

QnA Maker může podporovat webové stránky nejčastějších dotazů ve třech různých formulářích: Jednoduché stránky nejčastějších dotazů, stránky s nejčastějšími dotazy s odkazy, stránky s NEJČASTĚJŠÍmi dotazy a témata s tématy

### <a name="plain-faq-pages"></a>Jednoduché stránky – nejčastější dotazy

Toto je nejběžnější typ stránku s nejčastějšími dotazy, ve kterém odpovědi bezprostředně následuje dotazy na stejné stránce. 

Níže je příklad prostý stránku s nejčastějšími dotazy:

![Prostý příklad stránky – nejčastější dotazy pro znalostní báze](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Nejčastější dotazy týkající se stránky s odkazy 

V tomto typu stránku s nejčastějšími dotazy otázky jsou agregovány dohromady a jsou propojeny s odpovědí, které jsou v různých částech na stejné stránce nebo v různých stránkách.

Níže je příklad stránky – nejčastější dotazy s odkazy v části, které jsou na stejné stránce:

 ![Příklad stránky nejčastější dotazy týkající se propojení části pro znalostní báze](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Nejčastější dotazy týkající se stránky s domovskou stránku témata

Tento typ nejčastější dotazy k má Domovská stránka s tématy, kde každé téma se odkaz na jeho odpovídající maximálně na jiné stránce. Nástroj QnA Maker procházení tady, všechny připojené stránky k extrakci odpovídající otázky a odpovědi.

Níže je příklad stránky nejčastější dotazy k domovské stránce témata kde obsahuje odkazy na části Nejčastější dotazy týkající se různých stránkách. 

 ![Příklad stránky přímý odkaz na nejčastější dotazy pro znalostní báze](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Adresy URL podpory

QnA Maker může zpracovat částečně strukturované webové stránky podpory, jako jsou například webové články, které popisují, jak provést danou úlohu, jak diagnostikovat a vyřešit daný problém a jaké jsou osvědčené postupy pro daný proces. Extrakce funguje nejlépe na obsahu, který má jasně strukturu s hierarchickými nadpisy.

> [!NOTE]
> Extrakce článků podpory je nová funkce a je v rané fázi. Funguje nejlépe pro jednoduché stránky, které jsou dobře strukturované a neobsahují složitá záhlaví a zápatí.

![QnA Maker podporuje extrakci z částečně strukturovaných webových stránek tam, kde je k dispozici jasná struktura s hierarchickými nadpisy.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Soubor PDF / DOC soubory

Nástroj QnA Maker můžete zpracovat semistrukturovaná obsah v souboru PDF nebo dokumentu a převádět je do maximálně. Dobré soubor, který může být extrahována a je jedním obsah je uspořádaný v nějaké podobě strukturovaných kde je reprezentován v částech dobře definované. Oddíly lze dále rozdělit na témata nebo dílčí témata. Extrakce funguje nejlépe na dokumenty, které se mají Vymazat strukturu s hierarchické záhlaví.

Nástroj QnA Maker identifikuje oddíly a témata a vztahy v souboru neřiďte visual příčiny, jako je písmo, styl písma číslování, barvy atd. Příručky, nejčastější dotazy, pokyny, zásady, brožury, oznámení a mnoho dalších typů souborů, může být částečně strukturované soubory PDF nebo dokumentu. Níže jsou uvedeny některé typy příklad těchto souborů.

### <a name="product-manuals"></a>Produktových příruček

Ruční je obvykle pokyny materiál, který doprovází produktu. Umožňuje uživateli nastavit, použít, udržovat a řešit produktu. Nástroj QnA Maker zpracovává ruční, extrahuje položek a podpoložek jako dotazy a další obsah jako odpovědi. Podívejte se příklad [tady](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Tady je příklad příručky s indexovou stránku a hierarchické obsahu

 ![Ruční příklad znalostní bázi produktu](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extrakce funguje nejlépe na příručky, které mají tabulku obsah a/nebo indexovou stránku a Vymazat strukturu s hierarchické záhlaví.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, papírů a další soubory

Mnoho dalších typů dokumentů mohou být také zpracovány ke generování dvojice dotazů a odpovědí, za předpokladu, že máte Vymazat strukturu a rozložení. Mezi ně patří: Brožury, pokyny, sestavy, dokumenty White Paper, vědecké dokumenty, zásady, knihy atd. Podívejte se příklad [tady](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Níže je příklad částečně strukturovaných dokumentů, bez indexu:

 ![Azure Blob storage částečně strukturovaných dokumentů](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturovaný dokument QnA

Formát pro strukturované – odpovědi na otázky v souborech DOC, je ve formě střídavé otázky a odpovědi na každém řádku jednu otázku na řádek následovaný jeho odpovědí na následujícím řádku, jak je znázorněno níže: 

```text
Question1

Answer1

Question2

Answer2
```

Níže je příklad strukturovaných QnA wordový dokument:

 ![Strukturované příklad QnA dokument znalostní báze](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované *TXT*, *TSV* a *XLS* soubory

Strukturované maximálně ve formě *.txt*, *TSV* nebo *.xls* soubory se také dají nahrát pro nástroj QnA Maker k vytvoření nebo upravte znalostní báze.  Toto může být prostý text nebo může mít obsah ve formátu RTF nebo HTML. 

| Otázka  | Odpověď  | Metadata (1 klíč: 1 hodnota) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Žádné další sloupce ve zdrojovém souboru jsou ignorovány.

### <a name="example-of-structured-excel-file"></a>Příklad strukturovaného excelového souboru

Níže je příklad strukturovaných QnA *.xls* soubor s obsahem HTML:

 ![Strukturované QnA excel příklad znalostní báze](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Příklad alternativních otázek pro jednu odpověď v excelovém souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* , který má několik alternativních otázek pro jednu odpověď:

 ![Příklad alternativních otázek pro jednu odpověď v excelovém souboru](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po importu souboru se dvojice otázka a odpověď nachází ve znalostní bázi, jak je znázorněno níže:

 ![Snímek obrazovky s alternativními otázkami pro import jedné odpovědi do znalostní báze Knowledge Base](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat pomocí importu

Import znalostní báze nahradí obsah znalostní báze existující. Strukturované TSV soubor, který obsahuje informace o zdroji dat vyžaduje import. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Source| Metadata (1 klíč: 1 hodnota) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Znalostní báze pomocí redakčních úprav přidat

Pokud nemáte existující obsah k vyplnění znalostní báze, můžete přidat maximálně pomocí redakčních úprav v nástroje QnA Maker znalostní bázi. Zjistěte, jak aktualizovat znalostní báze [tady](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Důležité informace o formátování

Po importu souboru nebo adresy URL QnA Maker převádí a ukládá obsah ve [formátu Markdownu](https://en.wikipedia.org/wiki/Markdown). Proces převodu přidá do textu nové řádky, například `\n\n`. Znalost formátu Markdownu vám pomůže pochopit převedený obsah a spravovat obsah znalostní báze. 

Pokud přidáte nebo upravíte svůj obsah přímo ve znalostní bázi, použijte **formátování Markdownu** k vytvoření formátovaného textu nebo změňte obsah formátu Markdownu, který je již v odpovědi. QnA Maker podporuje většinu formátu Markdownu, aby bylo možné do obsahu dostat možnosti formátovaného textu. Klientská aplikace, jako je například robot pro chat, nemusí podporovat stejnou sadu formátů Markdownu. Je důležité otestovat zobrazení odpovědí klientské aplikace. 

Následuje seznam formátů Markdownu, které můžete použít v QnA Maker: 

|Účel|Formát|Příklad Markdownu|Vykreslování<br>Jak se zobrazuje v robotovi pro chat|
|--|--|--|--|
Nový řádek mezi 2 větami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![naformátovat nový řádek mezi dvěma větami](../media/qnamaker-concepts-datasources/format-newline.png)|
|Hlavičky z H1 do h6, počet `#` značících, která záhlaví jsou. 1 `#` je H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátování pomocí hlaviček Markdownu](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formátování pomocí hlaviček Markdownu H1 na H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kurzíva |`*text*`|`How do I create a bot with *QnA Maker*?`|![formátování pomocí kurzív](../media/qnamaker-concepts-datasources/format-italics.png)|
|Řetězec (tučně)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formát se silným označením pro tučné písmo](../media/qnamaker-concepts-datasources/format-strong.png)|
|Adresa URL odkazu|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formát pro adresu URL (hypertextový odkaz)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* Adresa URL pro veřejnou image|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formát pro adresu URL veřejného obrázku ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Přeškrtnutí|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formát pro přeškrtnutí](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Tučné a kurzívy|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formát pro tučné písmo a kurzívu](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Tučná adresa URL odkazu|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formát pro adresu URL tučného písma](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|Adresa URL pro odkaz na kurzívu|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formát pro adresu URL kurzívou](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Řídicí symboly Markdownu|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formát pro adresu URL kurzívou](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Seřazený seznam|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Předchozí příklad používá automatické číslování integrovaný do Markdownu.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Předchozí příklad používá explicitní číslování.|![formát pro uspořádaný seznam](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Neuspořádaný seznam|`\n * item1 \n * item2`<br>or<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![formát pro neuspořádaný seznam](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Vnořené seznamy|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Můžete vnořit seřazené a neuspořádané seznamy dohromady. Karta `\t`označuje úroveň odsazení podřízeného prvku.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formát pro vnořený seznam neuspořádaných](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formát pro vnořený uspořádaný seznam](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker nezpracovává bitovou kopii jakýmkoli způsobem. Je to role klientské aplikace pro vykreslení image. 

## <a name="editing-your-knowledge-base-locally"></a>Místní úprava znalostní báze

Po vytvoření znalostní báze se doporučuje provádět úpravy textu znalostní báze na [portálu QnA maker](https://qnamaker.ai)a nemusíte je exportovat a znovu importovat pomocí místních souborů. Nicméně může nastat situace, kdy budete muset znalostní bázi upravit místně. 

Vyexportujte znalostní bázi ze stránky **Nastavení** a pak upravte znalostní bázi v aplikaci Microsoft Excel. Pokud se rozhodnete použít jinou aplikaci k úpravě exportovaného souboru TSV, může aplikace způsobit chyby syntaxe, protože není plně kompatibilní s požadavky na TSV. Soubory TSV v aplikaci Microsoft Excel obecně nezavádějí žádné chyby formátování. 

Až budete s úpravami hotovi, znovu naimportujte soubor TSV ze stránky **Nastavení** . Tato akce zcela nahradí aktuální znalostní bázi importovanou znalostní bázi. 

## <a name="testing-your-markdown"></a>Testování Markdownu

Pomocí kurzu **[CommonMark](https://commonmark.org/help/tutorial/index.html)** ověřte své Markdownu. V tomto kurzu je funkce **vyzkoušet** pro rychlé kopírování a vkládání. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled služby QnA Maker](../Overview/overview.md)