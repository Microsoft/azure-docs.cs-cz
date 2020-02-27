---
title: Pokyny k importu formátu dokumentů – QnA Maker
description: Pochopení způsobu, jakým jsou typy adres URL použity pro import a vytvoření QnA sad.
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651836"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Pokyny pro formát importovaných dokumentů a adres URL

Projděte si tyto pokyny pro formátování, abyste získali nejlepší výsledky pro svůj obsah.

## <a name="formatting-considerations"></a>Důležité informace o formátování

Po importu souboru nebo adresy URL QnA Maker převádí a ukládá obsah ve [formátu Markdownu](https://en.wikipedia.org/wiki/Markdown). Proces převodu přidá nové řádky do textu, například `\n\n`. Znalost formátu Markdownu vám pomůže pochopit převedený obsah a spravovat obsah znalostní báze.

Pokud přidáte nebo upravíte svůj obsah přímo ve znalostní bázi, použijte **formátování Markdownu** k vytvoření formátovaného textu nebo změňte obsah formátu Markdownu, který je již v odpovědi. QnA Maker podporuje většinu formátu Markdownu, aby bylo možné do obsahu dostat možnosti formátovaného textu. Klientská aplikace, jako je například robot pro chat, nemusí podporovat stejnou sadu formátů Markdownu. Je důležité otestovat zobrazení odpovědí klientské aplikace.

## <a name="basic-document-formatting"></a>Základní formátování dokumentu

QnA Maker identifikuje oddíly a pododdíly a vztahy v souboru na základě vizuálních vzhledů, jako je:

* velikost písma
* styl písma
* Od
* barvy

|Příklady pro dokumenty|
|--|
||



## <a name="product-manuals"></a>Produktových příruček

Ruční je obvykle pokyny materiál, který doprovází produktu. Umožňuje uživateli nastavit, použít, udržovat a řešit produktu. Nástroj QnA Maker zpracovává ruční, extrahuje položek a podpoložek jako dotazy a další obsah jako odpovědi. [Tady se můžete](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)podívat na příklad.

Tady je příklad příručky s indexovou stránku a hierarchické obsahu

 ![Ruční příklad znalostní bázi produktu](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extrakce funguje nejlépe na příručky, které mají tabulku obsah a/nebo indexovou stránku a Vymazat strukturu s hierarchické záhlaví.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, papírů a další soubory

Mnoho dalších typů dokumentů mohou být také zpracovány ke generování dvojice dotazů a odpovědí, za předpokladu, že máte Vymazat strukturu a rozložení. Patří mezi ně: brožury, pokyny, sestavy, dokumenty White Paper, vědecké dokumenty, zásady, knihy atd. [Tady se můžete](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)podívat na příklad.

Níže je příklad částečně strukturovaných dokumentů, bez indexu:

 ![Azure Blob storage částečně strukturovaných dokumentů](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturovaný dokument QnA

Formát pro strukturované – odpovědi na otázky v souborech DOC, je ve formě střídavé otázky a odpovědi na každém řádku jednu otázku na řádek následovaný jeho odpovědí na následujícím řádku, jak je znázorněno níže:

```text
Question1

Answer1

Question2

Answer2
```

Níže je příklad strukturovaných QnA wordový dokument:

 ![Strukturované příklad QnA dokument znalostní báze](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované soubory *txt*, *TSV* a *XLS*

QnAs ve formě strukturovaných souborů *. txt*, *. TSV* nebo *. xls* je také možné odeslat QnA maker k vytvoření nebo rozšíření znalostní báze.  Toto může být prostý text nebo může mít obsah ve formátu RTF nebo HTML.

| Otázka  | Odpověď  | Metadata (1 klíč: 1 hodnota) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Žádné další sloupce ve zdrojovém souboru jsou ignorovány.

### <a name="example-of-structured-excel-file"></a>Příklad strukturovaného excelového souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* s obsahem HTML:

 ![Strukturované QnA excel příklad znalostní báze](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Příklad alternativních otázek pro jednu odpověď v excelovém souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* , který má několik alternativních otázek pro jednu odpověď:

 ![Příklad alternativních otázek pro jednu odpověď v excelovém souboru](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po importu souboru se dvojice otázka a odpověď nachází ve znalostní bázi, jak je znázorněno níže:

 ![Snímek obrazovky s alternativními otázkami pro import jedné odpovědi do znalostní báze Knowledge Base](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat pomocí importu

Import znalostní báze nahradí obsah znalostní báze existující. Strukturované TSV soubor, který obsahuje informace o zdroji dat vyžaduje import. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Vícenásobné formátování dokumentu

* Použijte záhlaví a dílčí záhlaví k označení hierarchie. Například můžete chtít, aby se QnAy, které jsou v nadřazeném prvku, a přiřadí se QnA, který by měl být proveden jako výzva. Použijte malou velikost nadpisu k označení další hierarchie. Nepoužívejte styly, barvy ani jiný mechanismus pro určení struktury v dokumentu, QnA Maker neextrahuje výzvy vícenásobného zapnutí.
* První znak nadpisu musí být velkými písmeny.
* Nekončit záhlavím otazníkem, `?`.


|Příklady pro dokumenty|
|--|
||