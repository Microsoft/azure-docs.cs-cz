---
title: Markdown formát - QnA Maker
description: Následuje seznam formátů markdownu, které můžete použít v textu odpovědi QnA Makeru.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045394"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formát Markdown podporovaný v textu odpovědi QnA Maker

QnA Maker ukládá text odpovědi jako markdown. Existuje mnoho chutí markdownu. Chcete-li se ujistit, že text odpovědi je vrácen a zobrazen správně, použijte tento odkaz.

Použijte kurz **[CommonMark](https://commonmark.org/help/tutorial/index.html)** k ověření markdownu. Výukový program má funkci **Try it** pro rychlé kopírování a vkládání ověření.

## <a name="supported-markdown-format"></a>Podporovaný formát značky

Následuje seznam formátů markdownu, které můžete použít v textu odpovědi QnA Makeru.

|Účel|Formát|Příklad Markdownu|Vykreslování<br>jak je zobrazeno v Chat bot|
|--|--|--|--|
Nová čára mezi dvěma větami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![formátování nového řádku mezi dvěma větami](./media/qnamaker-concepts-datasources/format-newline.png)|
|Záhlaví od h1 do h6, `#` počet označuje, které záhlaví. 1 `#` je h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátování se záhlavími značkdolů](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formát s hlavičkami markdownu H1 až H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kurzíva |`*text*`|`How do I create a bot with *QnA Maker*?`|![formát s kurzívou](./media/qnamaker-concepts-datasources/format-italics.png)|
|Silný (tučně)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formát se silným označením pro tučné](./media/qnamaker-concepts-datasources/format-strong.png)|
|Adresa URL pro odkaz|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formát adresy URL (hypertextový odkaz)](./media/qnamaker-concepts-datasources/format-url.png)|
|*ADRESA URL pro veřejný obrázek|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formát pro adresu URL veřejného obrázku ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Přeškrtnutí|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formát pro přeškrtnutí](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Tučné písmo a kurzíva|`***text***`|`How can I create a ***QnA Maker*** bot?`|![formát pro tučné písmo a kurzívu](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Tučná adresa URL pro odkaz|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formát pro tučnou adresu URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Adresa URL kurzívy pro odkaz|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formát adresy URL kurzívy](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Symboly únikových značek|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![formát adresy URL kurzívy](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Seřazený seznam|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Předchozí příklad používá automatické číslování integrované do markdownu.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Předchozí příklad používá explicitní číslování.|![formát pro seřazený seznam](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Neseřazený seznam|`\n * item1 \n * item2`<br>– nebo –<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formát pro neuspořádaný seznam](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Vnořené seznamy|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Seřazené a neuspořádané seznamy můžete vnořit společně. Karta , `\t`označuje úroveň odsazení podřízeného prvku.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formát vnořeného neuspořádaného seznamu](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formát vnořeného seřazeného seznamu](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

*QnA Maker obrázek žádným způsobem nezpracovává. Je role klientské aplikace k vykreslení bitové kopie.

Pokud chcete přidat obsah pomocí aktualizace/ nahrazení souborů API znalostní báze a obsah/soubor obsahuje značky HTML, můžete zachovat html v souboru tím, že zajistíte, aby otevření a zavření značek bylo převedeno v kódovaném formátu.

| Zachovat HTML  | Reprezentace v požadavku rozhraní API  | Zastoupení v KB |
|-----------|---------|-------------------------|
| Ano | \&Lt;br\&gt; | &lt;Br&gt; |
| Ano | \&lt;h3\&gt;záhlaví\&lt;/h3\&gt; | &lt;h3&gt;&lt;záhlaví /h3&gt; |

Cr LF(\r\n) jsou navíc převedeny na \n v KB. LF(\n) je udržována tak, jak je. Chcete-li uniknout jakékoli posloupnosti, jako je \t nebo \n,\\\\\\\\můžete použít\\\\zpětné lomítko, například: ' r n ' a ' t'

## <a name="next-steps"></a>Další kroky

Zkontrolujte [formáty souborů dávkového testování](reference-tsv-format-batch-testing.md).
