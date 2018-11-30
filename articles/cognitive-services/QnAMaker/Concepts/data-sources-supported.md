---
title: Zdroje dat podporované – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker automaticky extrahuje páry otázka – odpověď z částečně strukturovaných obsah, jako jsou nejčastější dotazy, produktových příruček, pokyny, podporu dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace Word. Obsah lze také přidat do znalostní báze ze strukturovaného QnA obsahu souborů.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/27/2018
ms.author: tulasim
ms.openlocfilehash: ee88c8dca1521f4cdc2e3ea141d5c1d497061bb4
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422350"
---
# <a name="data-sources-for-qna-maker-content"></a>Zdroje dat pro nástroj QnA Maker obsahu

Nástroj QnA Maker automaticky extrahuje páry otázka – odpověď z částečně strukturovaných obsah, jako jsou nejčastější dotazy, produktových příruček, pokyny, podporu dokumenty a zásady uložené jako webové stránky, soubory PDF nebo soubory dokumentů aplikace Word. Obsah lze také přidat do znalostní báze ze strukturovaného QnA obsahu souborů. 

Následující tabulka shrnuje typy obsahu a souboru formáty, které podporuje QnA Maker.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|zprostředkovatele identity|Nejčastější dotazy (fixní, oddíly nebo s domovskou stránku témata)|[Nejčastější dotazy týkající se prostý](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs), [– nejčastější dotazy s odkazy](https://www.microsoft.com/software-download/faq), [– nejčastější dotazy s témata domovské stránky](https://support.microsoft.com/products/windows?os=windows-10)|
|SOUBOR PDF / DOC|Nejčastější dotazy, příručka k produktu, brožury, dokument, leták zásad, podporu průvodce strukturovaných QnA atd.|[Strukturované QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [ukázky produktu Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf), [ukázkový částečně structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx), [ukázkový bílé paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Strukturované QnA souboru (včetně RTF, HTML podpora)|[Ukázka QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Strukturované QnA souboru|[Ukázka chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Umístění zdroje dat.

Pouze veřejné adresy URL jsou platné pro všechny datové zdroje. Neodesílejte zdroje dat, které vyžadují ověřování. Stáhněte si soubor z webu ověřený a pak pomocí možnosti nahrávání souborů extrakce otázek a odpovědí.

## <a name="faq-urls"></a>Nejčastější dotazy týkající se adresy URL

Nástroj QnA Maker může podporovat nejčastější dotazy k webové stránky ve 3 různých podob: prostý nejčastější dotazy k stránky, stránky – nejčastější dotazy s odkazy, nejčastější dotazy týkající se stránky s domovskou stránku témata.

### <a name="plain-faq-pages"></a>Jednoduché stránky – nejčastější dotazy

Toto je nejběžnější typ stránku s nejčastějšími dotazy, ve kterém odpovědi bezprostředně následuje dotazy na stejné stránce. 

Níže je příklad prostý stránku s nejčastějšími dotazy:

![Prostý stránku s nejčastějšími dotazy](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Nejčastější dotazy týkající se stránky s odkazy 

V tomto typu stránku s nejčastějšími dotazy otázky jsou agregovány dohromady a jsou propojeny s odpovědí, které jsou v různých částech na stejné stránce nebo v různých stránkách.

Níže je příklad stránky – nejčastější dotazy s odkazy v části, které jsou na stejné stránce:

 ![Stránku s nejčastějšími dotazy odkazu oddílu](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Nejčastější dotazy týkající se stránky s domovskou stránku témata

Tento typ nejčastější dotazy k má Domovská stránka s tématy, kde každé téma se odkaz na jeho odpovídající maximálně na jiné stránce. Nástroj QnA Maker procházení tady, všechny připojené stránky k extrakci odpovídající otázky a odpovědi.

Níže je příklad stránky nejčastější dotazy k domovské stránce témata kde obsahuje odkazy na části Nejčastější dotazy týkající se různých stránkách. 

 ![Stránka přímý odkaz na nejčastější dotazy](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>Soubor PDF / DOC soubory

Nástroj QnA Maker můžete zpracovat semistrukturovaná obsah v souboru PDF nebo dokumentu a převádět je do maximálně. Dobré soubor, který může být extrahována a je jedním obsah je uspořádaný v nějaké podobě strukturovaných kde je reprezentován v částech dobře definované. Oddíly lze dále rozdělit na témata nebo dílčí témata. Extrakce funguje nejlépe na dokumenty, které se mají Vymazat strukturu s hierarchické záhlaví.

Nástroj QnA Maker identifikuje oddíly a témata a vztahy v souboru neřiďte visual příčiny, jako je písmo, styl písma číslování, barvy atd. Příručky, nejčastější dotazy, pokyny, zásady, brožury, oznámení a mnoho dalších typů souborů, může být částečně strukturované soubory PDF nebo dokumentu. Níže jsou uvedeny některé typy příklad těchto souborů.

### <a name="product-manuals"></a>Produktových příruček

Ruční je obvykle pokyny materiál, který doprovází produktu. Umožňuje uživateli nastavit, použít, udržovat a řešit produktu. Nástroj QnA Maker zpracovává ruční, extrahuje položek a podpoložek jako dotazy a další obsah jako odpovědi. Podívejte se příklad [tady](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Tady je příklad příručky s indexovou stránku a hierarchické obsahu

 ![Příklad ruční produktu](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Extrakce funguje nejlépe na příručky, které mají tabulku obsah a/nebo indexovou stránku a Vymazat strukturu s hierarchické záhlaví.

### <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, papírů a další soubory

Mnoho dalších typů dokumentů mohou být také zpracovány ke generování dvojice dotazů a odpovědí, za předpokladu, že máte Vymazat strukturu a rozložení. Mezi ně patří: brožury pokyny, sestavy, bílý papír, vědecké práce, zásady, knihy, atd. Podívejte se příklad [tady](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

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

 ![Strukturovaný dokument QnA](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované *TXT*, *TSV* a *XLS* soubory

Strukturované maximálně ve formě *.txt*, *TSV* nebo *.xls* soubory se také dají nahrát pro nástroj QnA Maker k vytvoření nebo upravte znalostní báze.  Toto může být prostý text nebo může mít obsah ve formátu RTF nebo HTML. 

| Otázka  | Odpověď  | Metadata                |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Žádné další sloupce ve zdrojovém souboru jsou ignorovány.

Níže je příklad strukturovaných QnA *.xls* soubor s obsahem HTML:

 ![Strukturované QnA aplikace excel](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat pomocí importu

Import znalostní báze nahradí obsah znalostní báze existující. Strukturované TSV soubor, který obsahuje informace o zdroji dat vyžaduje import. Tyto informace pomáhají QnA Maker skupině páry otázka – odpověď a atribut pro konkrétní datové zdroje.

| Otázka  | Odpověď  | Zdroj| Metadata                |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Znalostní báze pomocí redakčních úprav přidat

Pokud nemáte existující obsah k vyplnění znalostní báze, můžete přidat maximálně pomocí redakčních úprav v nástroje QnA Maker znalostní bázi. Zjistěte, jak aktualizovat znalostní báze [tady](../How-To/edit-knowledge-base.md).

## <a name="formatting-considerations"></a>Důležité informace o formátování

Po importu souboru nebo adresy URL, je převeden na Markdown a uložená v tomto formátu. Pokud proces převodu není správně převod odkazů na soubory a adresy URL, měli upravovat dotazy a odpovědi na **upravit** stránky. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nastavení služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>Další informace najdete v tématech 

[Přehled služby QnA Maker](../Overview/overview.md)
