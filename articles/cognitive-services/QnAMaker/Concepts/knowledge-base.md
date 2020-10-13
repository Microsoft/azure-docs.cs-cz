---
title: Import ze zdrojů dat – QnA Maker
description: Naučte se importovat páry otázek a odpovědí ze zdrojů dat – QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5f609dc27aa1251cfad0249d26ef5140936bfe41
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776948"
---
# <a name="importing-from-data-sources"></a>Import ze zdrojů dat

Znalostní báze se skládá z párů otázek a odpovědí, které přidávají veřejné adresy URL a soubory.

## <a name="data-source-locations"></a>Umístění zdrojů dat

Obsah se načte do znalostní báze ze zdroje dat. Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověřování.

Pro [soubory služby SharePoint](../how-to/add-sharepoint-datasources.md)zabezpečené s ověřováním se jedná o výjimku. Prostředky SharePoint musí být soubory, nikoli webové stránky. Pokud adresa URL končí webovou příponou, například .ASPX, neimportuje se z SharePointu do služby QnA Maker.

## <a name="chit-chat-content"></a>Obsah chatu funkce CHITEST

Sada obsahu QnA chatu CHITEST se nabízí jako úplný zdroj dat obsahu v několika jazycích a ve stylu konverzací. To může být výchozí bod pro vaši osobnost robota a ušetří vám čas a náklady na jejich zápis od začátku. Přečtěte si, [jak přidat](../how-to/chit-chat-knowledge-base.md) tuto sadu obsahu do znalostní báze.

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný `.tsv` soubor, který obsahuje otázky a odpověď. Tyto informace pomáhají QnA Maker seskupovat páry dotazů a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturované formáty vícenásobného přepínání prostřednictvím importu

Můžete vytvořit vícenásobné konverzace ve `.tsv` formátu souboru. Tento formát vám umožní vytvořit vícenásobné konverzace tím, že analyzuje předchozí protokoly chatu (s ostatními procesy, nepoužívá QnA Maker) a pak vytvoří `.tsv` soubor prostřednictvím automatizace. Importujte soubor a nahraďte stávající znalostní bázi.

> [!div class="mx-imgBorder"]
> ![Koncepční model 3 úrovní vícefaktorového dotazování](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Zobrazí se sloupec pro vícenásobný tah `.tsv` , který je specifický pro vícenásobný výběr. **Prompts** Příklad `.tsv` zobrazený v Excelu zobrazuje informace, které se mají zahrnout pro definování vícenásobných podřízených objektů:

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

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Viz také

Použijte odkaz QnA Maker [Markdownu](../reference-markdown-format.md) , který vám umožní formátovat odpovědi.

[Přehled služby QnA Maker](../Overview/overview.md)

Vytvořte a upravte znalostní bázi pomocí:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Generovat odpověď s:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
