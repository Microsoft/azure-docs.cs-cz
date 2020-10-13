---
title: Pokyny k importu formátu dokumentů – QnA Maker
description: Pomocí těchto pokynů pro import dokumentů získáte nejlepší výsledky pro svůj obsah.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: d35ea57a68e500deffa99033b83114ec518dd5e3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776727"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Pokyny pro formát importovaných dokumentů a adres URL

Projděte si tyto pokyny pro formátování, abyste získali nejlepší výsledky pro svůj obsah.

## <a name="formatting-considerations"></a>Požadavky na formátování

Po importu souboru nebo adresy URL QnA Maker převádí a ukládá obsah ve [formátu Markdownu](https://en.wikipedia.org/wiki/Markdown). Proces převodu přidá do textu nové řádky, například `\n\n` . Znalost formátu Markdownu vám pomůže pochopit převedený obsah a spravovat obsah znalostní báze.

Pokud přidáte nebo upravíte svůj obsah přímo ve znalostní bázi, použijte **formátování Markdownu** k vytvoření formátovaného textu nebo změňte obsah formátu Markdownu, který je již v odpovědi. QnA Maker podporuje většinu formátu Markdownu, aby bylo možné do obsahu dostat možnosti formátovaného textu. Klientská aplikace, jako je například robot pro chat, nemusí podporovat stejnou sadu formátů Markdownu. Je důležité otestovat zobrazení odpovědí klientské aplikace.

Podívejte se na úplný seznam [typů obsahu a příklady](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Základní formátování dokumentu

QnA Maker identifikuje oddíly a pododdíly a vztahy v souboru na základě vizuálních vzhledů, jako je:

* velikost písma
* styl písma
* od
* barvy

## <a name="product-manuals"></a>Příručky k produktu

Ručním materiálem je obvykle doprovodný materiál, který doprovází produkt. Pomáhá uživateli nastavit, používat, udržovat a řešit potíže s produktem. Když QnA Maker zpracuje ručně, extrahuje záhlaví a dílčí hlavičky jako otázky a následný obsah jako odpovědi. [Tady se můžete](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)podívat na příklad.

Níže je příklad příručky se stránkou indexu a hierarchickým obsahem

 ![Ruční příklad produktu pro znalostní bázi](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extrakce funguje nejlépe na příručkách, které mají obsah nebo stránku indexu, a na jasné struktuře s hierarchickými nadpisy.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, dokumenty a další soubory

Mnoho dalších typů dokumentů je také možné zpracovat, aby se generovaly páry QA za předpokladu, že mají zřetelnou strukturu a rozložení. Patří mezi ně: brožury, pokyny, sestavy, dokumenty White Paper, vědecké dokumenty, zásady, knihy atd. [Tady se můžete](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)podívat na příklad.

Níže je příklad částečně strukturovaného dokumentu bez indexu:

 ![Částečně strukturovaný dokument úložiště objektů BLOB v Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturovaný QnA dokument

Formát strukturovaných Question-Answers v souborech DOC má formu střídání otázek a odpovědí na řádek, jednu otázku na řádek následovaný odpovědí na následujícím řádku, jak je znázorněno níže:

```text
Question1

Answer1

Question2

Answer2
```

Níže je uveden příklad strukturovaného dokumentu aplikace QnA:

 ![Příklad strukturovaného dokumentu QnA pro znalostní bázi](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované soubory *txt*, *TSV* a *XLS*

QnAs ve formě strukturovaných souborů *. txt*, *. TSV* nebo *. xls* je také možné odeslat QnA maker k vytvoření nebo rozšíření znalostní báze.  Může se jednat o prostý text nebo může mít obsah ve formátu RTF nebo HTML.

| Otázka  | Odpověď  | Metadata (1 klíč: 1 hodnota) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Všechny další sloupce ve zdrojovém souboru jsou ignorovány.

### <a name="example-of-structured-excel-file"></a>Příklad strukturovaného excelového souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* s obsahem HTML:

 ![Strukturovaný QnA excelový příklad pro znalostní bázi](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Příklad alternativních otázek pro jednu odpověď v excelovém souboru

Níže je uveden příklad strukturovaného souboru QnA *. xls* , který má několik alternativních otázek pro jednu odpověď:

 ![Příklad alternativních otázek pro jednu odpověď v excelovém souboru](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po importu souboru se dvojice otázka a odpověď nachází ve znalostní bázi, jak je znázorněno níže:

 ![Snímek obrazovky s alternativními otázkami pro import jedné odpovědi do znalostní báze Knowledge Base](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný soubor. TSV, který obsahuje informace o zdroji dat. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Vícenásobné formátování dokumentu

* Použijte záhlaví a dílčí záhlaví k označení hierarchie. Například můžete chtít, aby se QnAy, které jsou v nadřazeném prvku, a přiřadí se QnA, který by měl být proveden jako výzva. Použijte malou velikost nadpisu k označení další hierarchie. Nepoužívejte styly, barvy ani jiný mechanismus pro určení struktury v dokumentu, QnA Maker neextrahuje výzvy vícenásobného zapnutí.
* První znak nadpisu musí být velkými písmeny.
* Nekončit záhlavím otazníkem, `?` .

**Ukázkové dokumenty**:<br>[Plocha pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Výhody společnosti Contoso (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Výhody společnosti Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Další kroky

Zobrazit úplný seznam [typů obsahu a příklady](./Concepts/content-types.md#file-and-url-data-types)