---
title: Pokyny pro import formátu dokumentu – QnA Maker
description: Zjistěte, jak se typy adres URL používají k importu a vytváření párů QnA.
ms.topic: reference
ms.date: 04/06/2020
ms.openlocfilehash: 799e85e2200d3caa29c9b76bc57a62fc883d246d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804329"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>Pokyny pro formátování importovaných dokumentů a adres URL

Projděte si tyto pokyny pro formátování, abyste získali nejlepší výsledky pro váš obsah.

## <a name="formatting-considerations"></a>Důležité informace o formátování

Po importu souboru nebo adresy URL nástroj QnA Maker převede a uloží váš obsah ve [formátu markdownu](https://en.wikipedia.org/wiki/Markdown). Proces převodu přidá do textu nové `\n\n`řádky, například . Znalost formátu markdown vám pomůže pochopit převedený obsah a spravovat obsah znalostní báze.

Pokud přidáte nebo upravíte obsah přímo ve znalostní bázi, použijte **formátování markdownu** k vytvoření obsahu s formátem RTNebo ke změně obsahu ve formátu markdown, který je již v odpovědi. QnA Maker podporuje většinu formátu markdown, aby do vašeho obsahu přinesl funkce s formátovaným textem. Klientská aplikace, například chatovací robot, však nemusí podporovat stejnou sadu formátů markdownu. Je důležité otestovat zobrazení odpovědí klientské aplikace.

Podívejte se na úplný seznam [typů obsahu a příkladů](./Concepts/content-types.md#file-and-url-data-types).

## <a name="basic-document-formatting"></a>Základní formátování dokumentu

QnA Maker identifikuje oddíly a podsekce a vztahy v souboru na základě vizuálních vodítek, jako jsou:

* velikost písma
* styl písma
* Číslování
* Barvy

## <a name="product-manuals"></a>Příručky k produktům

Příručka je obvykle naváděcí materiál, který doprovází produkt. Pomáhá uživateli nastavit, používat, udržovat a řešit problémy s produktem. Když QnA Maker zpracuje manuál, extrahuje nadpisy a podnadpisy jako otázky a následný obsah jako odpovědi. Viz příklad [zde](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Níže je uveden příklad příručky se stránkou indexu a hierarchickým obsahem

 ![Příklad příručky k produktu pro znalostní bázi](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> Extrakce funguje nejlépe u příruček, které mají obsah nebo stránku indexu a jasnou strukturu s hierarchickými nadpisy.

## <a name="brochures-guidelines-papers-and-other-files"></a>Brožury, pokyny, dokumenty a další soubory

Mnoho dalších typů dokumentů lze také zpracovat pro generování párů QA za předpokladu, že mají jasnou strukturu a rozložení. Patří mezi ně: brožury, pokyny, zprávy, bílé knihy, vědecké dokumenty, politiky, knihy atd. Viz příklad [zde](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Níže je uveden příklad polostrukturovaného dokumentu bez indexu:

 ![Částečně strukturovaný doc úložiště objektů blob Azure](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>Strukturovaný dokument QnA

Formát pro strukturované odpovědi na otázky v souborech DOC je ve formě střídavých otázek a odpovědí na řádek, jedna otázka na řádek následovaná jeho odpovědí v následujícím řádku, jak je znázorněno níže:

```text
Question1

Answer1

Question2

Answer2
```

Níže je uveden příklad strukturovaného dokumentu qnA slovo:

 ![Příklad strukturovaného dokumentu QnA pro znalostní bázi](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>Strukturované *TXT*, *TSV* a *XLS* soubory

QnAs ve formě strukturovaných *souborů TXT*, *TSV* nebo *XLS* lze také nahrát do qnA makeru a vytvořit nebo rozšířit znalostní bázi.  Může se jedná buď o prostý text, nebo o obsah v RTF nebo HTML.

| Otázka  | Odpověď  | Metadata (1 klíč: 1 hodnota) |
|-----------|---------|-------------------------|
| Otázka 1 | Odpověď1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Otázka 2 | Odpověď2 |      `Key:Value`           |

Všechny další sloupce ve zdrojovém souboru jsou ignorovány.

### <a name="example-of-structured-excel-file"></a>Příklad strukturovaného souboru aplikace Excel

Níže je uveden příklad strukturovaného souboru QnA *.xls* s obsahem HTML:

 ![Strukturovaný příklad aplikace QnA excel pro znalostní bázi](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Příklad alternativních otázek pro jednu odpověď v excelovém souboru

Níže je uveden příklad strukturovaného souboru QnA *.xls* s několika alternativními otázkami pro jednu odpověď:

 ![Příklad alternativních otázek pro jednu odpověď v excelovém souboru](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

Po importu souboru je dvojice otázek a odpovědí ve znalostní bázi, jak je znázorněno níže:

 ![Snímek obrazovky s alternativními otázkami pro jednu odpověď importovo do znalostní báze](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Strukturovaný formát dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný soubor TSV, který obsahuje informace o zdroji dat. Tyto informace pomáhají qnA makeru seskupit dvojice odpovědí na otázky a přiřadit je k určitému zdroji dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Otázka 1 | Odpověď1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Otázka 2 | Odpověď2 | Redakční|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>Vícesměrné formátování dokumentů

* K označení hierarchie použijte nadpisy a podnadpisy. Například Můžete h1 označuje nadřazené QnA a h2 označuje QnA, které by měly být brány jako výzva. Malá velikost nadpisu označuje následnou hierarchii. Nepoužívejte styl, barvu nebo jiný mechanismus, který by naznačoval strukturu v dokumentu, QnA Maker nebude extrahovat vícesměrové výzvy.
* První znak nadpisu musí být velkými písmeny.
* Nezakončuj nadpis `?`otazníkem .

**Ukázkové dokumenty**:<br>[Surface Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Výhody společnosti Contoso (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Výhody společnosti Contoso (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)

## <a name="next-steps"></a>Další kroky

Zobrazení úplného seznamu [typů obsahu a příkladů](./Concepts/content-types.md#file-and-url-data-types)