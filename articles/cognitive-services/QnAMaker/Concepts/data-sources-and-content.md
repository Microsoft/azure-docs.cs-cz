---
title: Zdroje dat a typy obsahu – QnA Maker
description: Naučte se importovat páry otázek a odpovědí ze zdrojů dat a podporované typy obsahu, které zahrnují mnoho standardních strukturovaných dokumentů, jako jsou soubory PDF, DOCX a TXT-QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 0d4d32aba34a97c6a060c999694f66d79933d011
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99556050"
---
# <a name="importing-from-data-sources"></a>Import ze zdrojů dat

Znalostní báze se skládá z párů otázek a odpovědí, které přidávají veřejné adresy URL a soubory.

## <a name="data-source-locations"></a>Umístění zdrojů dat

Obsah se načte do znalostní báze ze zdroje dat. Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověřování.

Pro [soubory služby SharePoint](../how-to/add-sharepoint-datasources.md)zabezpečené s ověřováním se jedná o výjimku. Prostředky SharePoint musí být soubory, nikoli webové stránky. Pokud adresa URL končí webovou příponou, například .ASPX, neimportuje se z SharePointu do služby QnA Maker.

## <a name="chit-chat-content"></a>CHITEST – obsah chatu

Sada funkce CHITEST-chat obsahu se nabízí jako úplný zdroj dat obsahu v několika jazycích a ve stylu konverzací. To může být výchozí bod pro vaši osobnost robota a ušetří vám čas a náklady na jejich zápis od začátku. Naučte se přidat do znalostní báze [obsah funkce CHITEST-chat](../how-to/chit-chat-knowledge-base.md) .

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný `.tsv` soubor, který obsahuje otázky a odpovědi. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturované formáty vícenásobného přepínání prostřednictvím importu

Můžete vytvořit vícenásobné konverzace ve `.tsv` formátu souboru. Tento formát vám umožní vytvořit vícenásobné konverzace tím, že analyzuje předchozí protokoly chatu (s ostatními procesy, nepoužívá QnA Maker) a pak vytvoří `.tsv` soubor prostřednictvím automatizace. Importujte soubor a nahraďte stávající znalostní bázi.

> [!div class="mx-imgBorder"]
> ![Koncepční model 3 úrovní vícefaktorového dotazování](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Zobrazí se sloupec pro vícenásobný tah `.tsv` , který je specifický pro vícenásobný výběr.  Příklad `.tsv` zobrazený v Excelu zobrazuje informace, které se mají zahrnout pro definování vícenásobných podřízených objektů:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** je číslo a **zobrazenýtext** je text, který by neměl zahrnovat Markdownu.

> [!div class="mx-imgBorder"]
> ![Příklad dotazu s vícenásobným zapnutím, jak je znázorněno v Excelu](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportovat jako příklad

Pokud si nejste jistí, jak vyjádřit dvojici QnA v `.tsv` souboru:
* Použít tento [příklad ke stažení z GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Nebo vytvořte dvojici na portálu QnA Maker, uložte a pak exportujte znalostní bázi, kde najdete příklad toho, jak dvojici představovat.

## <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Typy obsahu dokumentů, které můžete přidat do znalostní báze
Mezi typy obsahu patří mnoho standardních strukturovaných dokumentů, jako jsou soubory PDF, DOC a TXT.

### <a name="file-and-url-data-types"></a>Datové typy souborů a adres URL

Následující tabulka shrnuje typy obsahu a formátů souborů, které jsou podporovány nástrojem QnA Maker.

|Typ zdroje|Typ obsahu| Příklady|
|--|--|--|
|URL|Nejčastější dotazy<br> (Plochý, s oddíly nebo s domovskou stránkou témat)<br>Stránky podpory <br> (Články s návody na jednu stranu, články týkající se řešení potíží atd.)|[Jednoduché Nejčastější dotazy](../troubleshooting.md) <br>[Nejčastější dotazy s odkazy](https://www.microsoft.com/en-us/software-download/faq)<br> [Nejčastější dotazy s domovskou stránkou s tématy](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Článek podpory](./best-practices.md)|
|PDF/DOC|Nejčastější<br> Ruční produkt,<br> Brožury,<br> Formátech<br> Zásady oznámení<br> Průvodce podporou,<br> Strukturované QnA,<br> Cokoli dalšího|**Bez vícenásobného zapnutí**<br>[Strukturované QnA.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx),<br> [Ukázkový produkt Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf),<br> [Ukázka semi-structured.docx](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx),<br> [Ukázka bílého paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Vícenásobné zapínání**:<br>[Plocha pro (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Výhody společnosti Contoso (DOCX)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Výhody společnosti Contoso (PDF)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|Strukturovaný soubor QnA<br> (včetně formátu RTF, HTML support)|**Bez vícenásobného zapínání**:<br>[Ukázka FAQ.xlsQnA ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**Vícenásobné zapínání**:<br>[Strukturované jednoduché FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[FAQ.xlsSurface přenosného počítače ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|Strukturovaný soubor QnA|[Ukázka Chit-Chat. TSV](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Pokud pro zdroj dat potřebujete ověření, zvažte následující metody pro získání tohoto obsahu do QnA Maker:

* Stáhnout soubor ručně a importovat do QnA Maker
* Přidat soubor z ověřeného [umístění služby SharePoint](../how-to/add-sharepoint-datasources.md)

### <a name="url-content"></a>Obsah adresy URL

Pomocí **adresy URL** v QnA maker lze importovat dva typy dokumentů:

* Adresy URL pro nejčastější dotazy
* Adresy URL podpory

Každý typ indikuje očekávaný formát.

### <a name="file-based-content"></a>Obsah založený na souborech

Do znalostní báze můžete přidat soubory z veřejného zdroje nebo místního systému souborů na [portálu QnA maker](https://www.qnamaker.ai).

### <a name="content-format-guidelines"></a>Pokyny k formátu obsahu

Přečtěte si další informace o [pokynech pro formát](../reference-document-format-guidelines.md) pro různé soubory.

## <a name="next-steps"></a>Další kroky

Naučte se [upravovat QnAs](../how-to/edit-knowledge-base.md).
