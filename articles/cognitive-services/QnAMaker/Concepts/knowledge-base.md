---
title: Import ze zdrojů dat – QnA Maker
description: QnA Maker znalostní báze se skládá ze sady sad otázek a odpovědí (QnA) a volitelná Metadata přidružená ke každému páru QnA.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389154"
---
# <a name="importing-from-data-sources"></a>Import ze zdrojů dat

Znalostní báze se skládá z sad otázek a odpovědí, které zadávají veřejné adresy URL a soubory.

## <a name="data-source-locations"></a>Umístění zdroje dat.

Obsah se načte do znalostní báze ze zdroje dat. Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověřování.

Pro [soubory služby SharePoint](../how-to/add-sharepoint-datasources.md)zabezpečené s ověřováním se jedná o výjimku. Prostředky SharePoint musí být soubory, nikoli webové stránky. V případě, že adresa URL končí rozšířením web, například. ASPX se nebude naimportovat do QnA Maker ze SharePointu.

## <a name="chit-chat-content"></a>Obsah chatu funkce CHITEST

Sada obsahu QnA chatu CHITEST se nabízí jako úplný zdroj dat obsahu v několika jazycích a ve stylu konverzací. To může být výchozím bodem pro posouzení vašich osobnostních svého robota, a to vám ušetří čas a náklady na jejich vytváření od začátku. Přečtěte si, [jak přidat](../how-to/chit-chat-knowledge-base.md) tuto sadu obsahu do znalostní báze.

## <a name="structured-data-format-through-import"></a>Formát strukturovaných dat pomocí importu

Import znalostní báze nahradí obsah znalostní báze existující. Import vyžaduje strukturovaný `.tsv` soubor, který obsahuje otázky a odpověď. Tyto informace pomáhají QnA Maker skupinové sady pro otázky a jejich atributy na konkrétní zdroj dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Redakční|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturované formáty vícenásobného přepínání prostřednictvím importu

Můžete vytvořit vícenásobné konverzace ve formátu souboru `.tsv`. Tento formát vám umožní vytvořit vícenásobné konverzace analýzou předchozích protokolů chatu (s jinými procesy, bez použití QnA Maker) a pak vytvořit soubor `.tsv` prostřednictvím automatizace. Importujte soubor a nahraďte stávající znalostní bázi.

> [!div class="mx-imgBorder"]
> ![koncepční model 3 úrovní](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)ch otázek s vícenásobným zapnutím

**Zobrazí**se sloupec pro `.tsv`s vícenásobným zahnutím, který je specifický pro vícenásobný výběr. Příklad `.tsv`, zobrazený v Excelu, zobrazuje informace, které se mají zahrnout pro definování vícenásobného navýšení podřízených objektů:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** je číslo a **zobrazenýtext** je text, který by neměl zahrnovat Markdownu.

> [!div class="mx-imgBorder"]
> Příklad ![s vícenásobným zapnutím, jak je znázorněno v Excelu](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportovat jako příklad

Pokud si nejste jistí, jak reprezentovat QnA sadu v souboru `.tsv`, vytvořte sadu na portálu QnA Maker, uložte a pak exportujte znalostní bázi pro příklad způsobu reprezentace sady.

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
