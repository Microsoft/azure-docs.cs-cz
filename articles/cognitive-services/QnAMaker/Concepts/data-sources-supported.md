---
title: Podporované zdroje dat – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker automaticky extrahuje páry otázek a odpovědí z částečně strukturovaného obsahu, jako jsou nejčastější dotazy, příručky k produktům, pokyny, podpůrné dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace MS Word. Obsah se taky dá do znalostní báze přidat ze strukturovaných souborů obsahu QnA.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: diberry
ms.openlocfilehash: 1a9f3eb0ea79a0cd79850e721d081b00dc582a31
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695278"
---
# <a name="data-sources-for-qna-maker-content"></a>Zdroje dat pro QnA Maker obsah

QnA Maker automaticky extrahuje páry otázek a odpovědí z částečně strukturovaného obsahu, jako jsou nejčastější dotazy, příručky k produktům, pokyny, podpůrné dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace MS Word. Obsah se taky dá do znalostní báze přidat ze strukturovaných souborů obsahu QnA. 

<a name="data-types"></a>

## <a name="file-and-url-data-types"></a>Datové typy souborů a adres URL

Následující tabulka shrnuje typy obsahu a formátů souborů, které jsou podporovány nástrojem QnA Maker.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|Adresa URL|Nejčastější dotazy<br> (Plochý, s oddíly nebo s domovskou stránkou témat)<br>Stránky podpory <br> (Články s návody na jednu stranu, články týkající se řešení potíží atd.)|[Jednoduché Nejčastější dotazy](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Nejčastější dotazy s odkazy](https://www.microsoft.com/software-download/faq)<br> [Nejčastější dotazy s domovskou stránkou s tématy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Článek podpory](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Nejčastější<br> Ruční produkt,<br> brožury,<br> Formátech<br> Zásady oznámení<br> Průvodce podporou,<br> Strukturované QnA,<br> Atd.|[Strukturované QnA. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka ručního produktu. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Ukázka semi-Structured. doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka dokumentu White Paper. PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br>[Ukázka multi-Turn. docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|Strukturovaný soubor QnA<br> (včetně formátu RTF, HTML support)|[Ukázka nejčastějších dotazů QnA. xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|Strukturovaný soubor QnA|[Ukázka Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

**Soubory TSV a XLS**z exportovaných základů znalostní báze lze použít pouze importem souborů ze stránky **nastavení** na portálu QnA maker. Nelze je použít jako zdroje dat během vytváření znalostní báze nebo pomocí funkce Přidat soubor nebo adresu URL na stránce **Nastavení** . 

Přečtěte si další informace o používání [multi-Turn docs](). 

## <a name="data-source-locations"></a>Umístění zdrojů dat

Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověřování. 

Pokud potřebujete ověření pro zdroj dat, můžete k získání těchto dat QnA Maker použít následující metody:

* [Stáhnout soubor ručně](#download-file-from-authenticated-data-source-location) a importovat do QnA maker
* Importovat soubor pro ověřené [umístění služby SharePoint](#import-file-from-authenticated-sharepoint) 

### <a name="download-file-from-authenticated-data-source-location"></a>Stáhnout soubor z umístění ověřeného zdroje dat

Pokud máte ověřený soubor (ne v ověřeném umístění služby SharePoint) nebo adresu URL, je alternativním parametrem stažení souboru z ověřené lokality do místního počítače a následné přidání souboru z místního počítače do znalostní báze.

### <a name="import-file-from-authenticated-sharepoint"></a>Importovat soubor z ověřeného SharePointu 

[Umístění zdrojů dat služby SharePoint](../How-to/add-sharepoint-datasources.md) můžou poskytovat ověřené **soubory**. Prostředky SharePoint musí být soubory, nikoli webové stránky. V případě, že adresa URL končí rozšířením web, například **. ASPX**se nebude naimportovat do QnA maker ze SharePointu.


## <a name="faq-urls"></a>Adresy URL pro nejčastější dotazy

QnA Maker může podporovat webové stránky NEJČASTĚJŠÍch dotazů ve třech různých formulářích: časté stránky NEJČASTĚJŠÍch dotazů, časté stránky s odkazy, nejčastější dotazy a stránky s tématy.

### <a name="plain-faq-pages"></a>Jednoduché stránky nejčastějších dotazů

Toto je nejběžnější typ stránky s nejčastějšími dotazy, ve kterém odpovědi okamžitě následují na stejné stránce. 

Níže je uveden příklad jednoduché stránky Nejčastější dotazy:

![Příklad stránky s běžnými nejčastějšími dotazy pro znalostní bázi](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Stránky nejčastějších dotazů s odkazy 

V tomto typu stránky Nejčastější dotazy se otázky agreguje společně a jsou propojeny s odpověďmi, které jsou buď v různých částech stejné stránky, nebo na různých stránkách.

Níže je uveden příklad stránky Nejčastější dotazy s odkazy v sekcích, které jsou na stejné stránce:

 ![Příklad stránky s odkazy na oddíly pro znalostní bázi Knowledge Base](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Stránky s nejčastějšími dotazy s domovskou stránkou témat

Tento typ nejčastějších dotazů má domovskou stránku s tématy, kde každé téma obsahuje odkaz na příslušné QnAs na jiné stránce. Tady QnA Maker prochází všechny propojené stránky a extrahuje příslušné otázky & odpovědi.

Níže je uveden příklad stránky s nejčastějšími dotazy, kde se na domovské stránce témata nacházejí odkazy na oddíly s nejčastějšími dotazy na různých stránkách. 

 ![Příklad stránky s přímým odkazem pro znalostní bázi Knowledge Base](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>Adresy URL podpory

QnA Maker může zpracovat částečně strukturované webové stránky podpory, jako jsou například webové články, které popisují, jak provést danou úlohu, jak diagnostikovat a vyřešit daný problém a jaké jsou osvědčené postupy pro daný proces. Extrakce funguje nejlépe na obsahu, který má jasně strukturu s hierarchickými nadpisy.

> [!NOTE]
> Extrakce článků podpory je nová funkce a je v rané fázi. Funguje nejlépe pro jednoduché stránky, které jsou dobře strukturované a neobsahují složitá záhlaví a zápatí.

![QnA Maker podporuje extrakci z částečně strukturovaných webových stránek tam, kde je k dispozici jasná struktura s hierarchickými nadpisy.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>Soubory PDF/DOC

QnA Maker může zpracovat částečně strukturovaný obsah v souboru PDF nebo souboru DOC a převést ho na QnAs. Dobrý soubor, který může být extrahován, je jeden, kde je obsah uspořádán v některém strukturovaném tvaru a je reprezentován v dobře definovaných oddílech. Oddíly mohou být dále rozděleny do pododdílů nebo dílčích témat. Extrakce funguje nejlépe na dokumentech, které mají jasné struktury s hierarchickými nadpisy.

QnA Maker identifikuje oddíly a pododdíly a vztahy v souboru na základě vizuálních vzhledů, jako je velikost písma, styl písma, číslování, barvy atd. Částečně strukturované soubory PDF nebo DOC můžou být příručky, nejčastější dotazy, pokyny, zásady, brožury, letáky a mnoho dalších typů souborů. Níže jsou uvedeny některé příklady typů těchto souborů.

### <a name="product-manuals"></a>Příručky k produktu

Ručním materiálem je obvykle doprovodný materiál, který doprovází produkt. Pomáhá uživateli nastavit, používat, udržovat a řešit potíže s produktem. Když QnA Maker zpracuje ručně, extrahuje záhlaví a dílčí hlavičky jako otázky a následný obsah jako odpovědi. [Tady se můžete](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)podívat na příklad.

Níže je příklad příručky se stránkou indexu a hierarchickým obsahem

 ![Ruční příklad produktu pro znalostní bázi](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extrakce funguje nejlépe na příručkách, které mají obsah nebo stránku indexu, a na jasné struktuře s hierarchickými nadpisy.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, dokumenty a další soubory

Mnoho dalších typů dokumentů je také možné zpracovat, aby se generovaly páry QA za předpokladu, že mají zřetelnou strukturu a rozložení. Patří mezi ně: brožury, pokyny, sestavy, dokumenty White Paper, vědecké dokumenty, zásady, knihy atd. [Tady se můžete](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)podívat na příklad.

Níže je příklad částečně strukturovaného dokumentu bez indexu:

 ![Částečně strukturovaný dokument úložiště objektů BLOB v Azure](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Strukturovaný QnA dokument

Formát strukturovaných odpovědí na otázky v souborech DOC má formu střídání otázek a odpovědí na řádek, jednu otázku na řádek následovaný odpovědí na následujícím řádku, jak je znázorněno níže: 

```text
Question1

Answer1

Question2

Answer2
```

Níže je uveden příklad strukturovaného dokumentu aplikace QnA:

 ![Příklad strukturovaného dokumentu QnA pro znalostní bázi](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované soubory *txt*, *TSV* a *XLS*

QnAs ve formě strukturovaných souborů *. txt*, *. TSV* nebo *. xls* je také možné odeslat QnA maker k vytvoření nebo rozšíření znalostní báze.  Může se jednat o prostý text nebo může mít obsah ve formátu RTF nebo HTML. 

| Daná  | Zodpovědět  | Metadata (1 klíč: 1 hodnota) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Všechny další sloupce ve zdrojovém souboru jsou ignorovány.

### <a name="example-of-structured-excel-file"></a>Příklad strukturovaného excelového souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* s obsahem HTML:

 ![Strukturovaný QnA excelový příklad pro znalostní bázi](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Příklad alternativních otázek pro jednu odpověď v excelovém souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* , který má několik alternativních otázek pro jednu odpověď:

 ![Příklad alternativních otázek pro jednu odpověď v excelovém souboru](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po importu souboru se dvojice otázka a odpověď nachází ve znalostní bázi, jak je znázorněno níže:

 ![Snímek obrazovky s alternativními otázkami pro import jedné odpovědi do znalostní báze Knowledge Base](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný soubor. TSV, který obsahuje informace o zdroji dat. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Daná  | Zodpovědět  | Zdroj| Metadata (1 klíč: 1 hodnota) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Redakční přidání do znalostní báze

Pokud nemáte již existující obsah k naplnění znalostní báze, můžete do QnA Maker znalostní báze přidat QnAs. Přečtěte si, jak [tady](../How-To/edit-knowledge-base.md)aktualizovat znalostní bázi.

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Požadavky na formátování

Po importu souboru nebo adresy URL QnA Maker převádí a ukládá obsah ve [formátu Markdownu](https://en.wikipedia.org/wiki/Markdown). Proces převodu přidá do textu nové řádky, například `\n\n`. Znalost formátu Markdownu vám pomůže pochopit převedený obsah a spravovat obsah znalostní báze. 

Pokud přidáte nebo upravíte svůj obsah přímo ve znalostní bázi, použijte **formátování Markdownu** k vytvoření formátovaného textu nebo změňte obsah formátu Markdownu, který je již v odpovědi. QnA Maker podporuje většinu formátu Markdownu, aby bylo možné do obsahu dostat možnosti formátovaného textu. Klientská aplikace, jako je například robot pro chat, nemusí podporovat stejnou sadu formátů Markdownu. Je důležité otestovat zobrazení odpovědí klientské aplikace. 

Následuje seznam formátů Markdownu, které můžete použít v QnA Maker: 

|Účel|Formát|Příklad Markdownu|vykreslování<br>Jak se zobrazuje v robotovi pro chat|
|--|--|--|--|
Nový řádek mezi 2 větami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![naformátovat nový řádek mezi dvěma větami](../media/qnamaker-concepts-datasources/format-newline.png)|
|Hlavičky z H1 do h6, počet `#` značí, které záhlaví. 1 `#` je H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátování pomocí hlaviček Markdownu](../media/qnamaker-concepts-datasources/format-headers.png)<br>![formátování pomocí hlaviček Markdownu H1 na H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kurzívu |`*text*`|`How do I create a bot with *QnA Maker*?`|![formátování pomocí kurzív](../media/qnamaker-concepts-datasources/format-italics.png)|
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
|Vnořené seznamy|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Můžete vnořit seřazené a neuspořádané seznamy dohromady. Karta `\t` označuje úroveň odsazení podřízeného prvku.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formát pro vnořený seznam neuspořádaných](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formát pro vnořený uspořádaný seznam](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker nezpracovává bitovou kopii jakýmkoli způsobem. Je to role klientské aplikace pro vykreslení image. 

Pokud chcete přidat obsah pomocí rozhraní API pro shodu aktualizací nebo nahrazení a obsah/soubor obsahuje značky HTML, můžete zachovat soubor HTML v souboru tím, že zajistíte, aby se otevírání a zavírání značek převedlo v kódovaném formátu.

| Zachovat HTML  | Reprezentace v žádosti rozhraní API  | Reprezentace v KB |
|-----------|---------|-------------------------|
| Ano | \&LT; br @ no__t-1gt; | @no__t – 0br @ no__t-1 |
| Ano | \&LT; H3 @ no__t-1gt; Header @ no__t-2LT;/H3 @ no__t-3gt; | &lt;h3 @ no__t-1header @ no__t-2/H3 @ no__t-3 |

Znaky CR (\r\n) se navíc v KB převádějí na \n. LF (\n) se udržuje tak, jak je. Pokud chcete řídicí sekvenci Escape, třeba \t nebo \n, můžete použít zpětné lomítko, například: "\\ @ no__t-1R @ no__t-2 @ no__t-3N" a "\\ @ no__t-5t"

## <a name="editing-your-knowledge-base-locally"></a>Místní úprava znalostní báze

Po vytvoření znalostní báze se doporučuje provádět úpravy textu znalostní báze na [portálu QnA maker](https://qnamaker.ai)a nemusíte je exportovat a znovu importovat pomocí místních souborů. Nicméně může nastat situace, kdy budete muset znalostní bázi upravit místně. 

Vyexportujte znalostní bázi ze stránky **Nastavení** a pak upravte znalostní bázi v aplikaci Microsoft Excel. Pokud se rozhodnete použít jinou aplikaci k úpravě exportovaného souboru TSV, může aplikace způsobit chyby syntaxe, protože není plně kompatibilní s požadavky na TSV. Soubory TSV v aplikaci Microsoft Excel obecně nezavádějí žádné chyby formátování. 

Až budete s úpravami hotovi, znovu naimportujte soubor TSV ze stránky **Nastavení** . Tato akce zcela nahradí aktuální znalostní bázi importovanou znalostní bázi. 

## <a name="testing-your-markdown"></a>Testování Markdownu

Pomocí kurzu **[CommonMark](https://commonmark.org/help/tutorial/index.html)** ověřte své Markdownu. V tomto kurzu je funkce **vyzkoušet** pro rychlé kopírování a vkládání. 

## <a name="version-control-for-data-in-your-knowledge-base"></a>Správa verzí pro data ve znalostní bázi

Správa verzí pro data je k dispozici prostřednictvím [funkce importu/exportu](development-lifecycle-knowledge-base.md#version-control-of-a-knowledge-base) na stránce **Nastavení** . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Viz také: 

[Přehled QnA Maker](../Overview/overview.md)
