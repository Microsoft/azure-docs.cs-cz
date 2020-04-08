---
title: Import ze zdrojů dat - QnA Maker
description: Znalostní báze QnA Maker se skládá ze sady otázek a odpovědí (QnA) a volitelných metadat přidružených ke každé dvojici QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804278"
---
# <a name="importing-from-data-sources"></a>Import ze zdrojů dat

Znalostní báze se skládá ze sad otázek a odpovědí, které přinesly veřejné adresy URL a soubory.

## <a name="data-source-locations"></a>Umístění zdrojů dat

Obsah je přenesen do znalostní báze ze zdroje dat. Umístění zdrojů dat jsou **veřejné adresy URL nebo soubory**, které nevyžadují ověření.

Výjimkou jsou [soubory služby SharePoint](../how-to/add-sharepoint-datasources.md)zabezpečené ověřováním. Prostředky služby SharePoint musí být soubory, nikoli webové stránky. Pokud adresa URL končí webovým rozšířením, například . ASPX, nebude importovat do QnA Maker ze SharePointu.

## <a name="chit-chat-content"></a>Chit chat obsah

Sada obsahu Chit chat QnA je nabízena jako kompletní zdroj dat obsahu v několika jazycích a konverzačních stylech. To může být výchozím bodem pro osobnost vašeho robota, a to vám ušetří čas a náklady na psaní je od nuly. Přečtěte [si, jak přidat](../how-to/chit-chat-knowledge-base.md) tuto sadu obsahu do znalostní báze.

## <a name="structured-data-format-through-import"></a>Strukturovaný formát dat prostřednictvím importu

Import znalostní báze nahrazuje obsah stávající znalostní báze. Import vyžaduje strukturovaný `.tsv` soubor, který obsahuje otázky a odpovědi. Tyto informace pomáhají qnA makeru seskupit sady odpovědí na otázky a přiřadit je k určitému zdroji dat.

| Otázka  | Odpověď  | Zdroj| Metadata (1 klíč: 1 hodnota) |
|-----------|---------|----|---------------------|
| Otázka 1 | Odpověď1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Otázka 2 | Odpověď2 | Redakční|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Strukturovaný vícesměrový formát prostřednictvím importu

Můžete vytvářet vícesměrové konverzace `.tsv` ve formátu souboru. Formát poskytuje možnost vytvářet vícenásobné konverzace analýzou předchozích protokolů chatu (s jinými procesy, `.tsv` které nepoužívají QnA Maker), a pak vytvořit soubor prostřednictvím automatizace. Importujte soubor a nahraďte stávající znalostní bázi.

> [!div class="mx-imgBorder"]
> ![Koncepční model 3 úrovní vícesměrové otázky](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Sloupec pro víceotočení `.tsv`, specifické pro víceotočení, je **Výzvy**. Příklad `.tsv`, zobrazený v aplikaci Excel, zobrazí informace, které mají být zahrnuty, a definují vícesměrové podřízené objekty:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** je číselný a **displayText** je text, který by neměl obsahovat markdown.

> [!div class="mx-imgBorder"]
> ![Příklad vícesměrové otázky, jak je znázorněno v Excelu](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Exportovat jako příklad

Pokud si nejste jisti, jak reprezentovat dvojici QnA v souboru: `.tsv`
* Použijte tento [příklad ke stažení z GitHubu](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Nebo vytvořte sadu na portálu QnA Maker, uložte a pak exportujte znalostní bázi pro příklad, jak sadu reprezentovat.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Životní cyklus vývoje znalostní báze](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>Viz také

Použijte odkaz QnA Maker [Markdown,](../reference-markdown-format.md) který vám pomůže formátovat odpovědi.

[Přehled služby QnA Maker](../Overview/overview.md)

Vytvořte a upravte znalostní bázi pomocí:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Vygenerujte odpověď pomocí:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
