---
title: Formát Markdownu – QnA Maker
description: Následuje seznam formátů Markdownu, které můžete použít v textu odpovědi QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 03/19/2020
ms.openlocfilehash: f49e05578f55a38845acbd4010f928fb17c51606
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96352265"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Formát Markdownu podporovaný v textu odpovědi QnA Maker

QnA Maker ukládá text odpovědi jako Markdownu. Existuje mnoho typů Markdownu. Chcete-li se ujistit, že je text odpovědi vrácen a zobrazen správně, použijte tento odkaz.

Pomocí kurzu **[CommonMark](https://commonmark.org/help/tutorial/index.html)** ověřte své Markdownu. V tomto kurzu je funkce **vyzkoušet** pro rychlé kopírování a vkládání.

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Kdy použít úpravy formátovaného textu oproti Markdownu

[S bohatou úpravou](How-To/edit-knowledge-base.md#add-an-editorial-qna-set) odpovědí můžete jako autor použít panel formátování k rychlému výběru a formátování textu.

Markdownu je lepší nástroj, pokud potřebujete automaticky vygenerovat obsah a vytvořit znalostní bázi, který se má importovat jako součást kanálu CI/CD nebo pro [dávkové testování](./index.yml).

## <a name="supported-markdown-format"></a>Podporovaný formát Markdownu

Následuje seznam formátů Markdownu, které můžete použít v textu odpovědi QnA Maker.

|Účel|Formát|Příklad Markdownu|Vykreslování<br>Jak se zobrazuje v robotovi pro chat|
|--|--|--|--|
Nový řádek mezi 2 větami.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![naformátovat nový řádek mezi dvěma větami](./media/qnamaker-concepts-datasources/format-newline.png)|
|Hlavičky z H1 do h6, počet `#` značících, která záhlaví jsou. 1 `#` je H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![formátování pomocí hlaviček Markdownu](./media/qnamaker-concepts-datasources/format-headers.png)<br>![formátování pomocí hlaviček Markdownu H1 na H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Kurzíva |`*text*`|`How do I create a bot with *QnA Maker*?`|![formátování pomocí kurzív](./media/qnamaker-concepts-datasources/format-italics.png)|
|Strong (tučné)|`**text**`|`How do I create a bot with **QnA Maker**?`|![formát se silným označením pro tučné písmo](./media/qnamaker-concepts-datasources/format-strong.png)|
|Adresa URL odkazu|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![formát pro adresu URL (hypertextový odkaz)](./media/qnamaker-concepts-datasources/format-url.png)|
|* Adresa URL pro veřejnou image|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![formát pro adresu URL veřejného obrázku ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Přeškrtnutí|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![formát pro přeškrtnutí](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Tučné a kurzívy|`***text**_`|`How can I create a _*_QnA Maker_** bot?`|![formát pro tučné písmo a kurzívu](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Tučná adresa URL odkazu|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![formát pro adresu URL tučného písma](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Adresa URL pro odkaz na kurzívu|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![formát pro adresu URL kurzívou](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Řídicí symboly Markdownu|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![Formát pro řídicí symboly Markdownu](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Seřazený seznam|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>Předchozí příklad používá automatické číslování integrovaný do Markdownu.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>Předchozí příklad používá explicitní číslování.|![formát pro uspořádaný seznam](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Neseřazený seznam|`\n * item1 \n * item2`<br>nebo<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![formát pro neuspořádaný seznam](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Vnořené seznamy|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Můžete vnořit seřazené a neuspořádané seznamy dohromady. Karta `\t` označuje úroveň odsazení podřízeného prvku.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![formát pro vnořený seznam neuspořádaných](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![formát pro vnořený uspořádaný seznam](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

* QnA Maker nezpracovává bitovou kopii jakýmkoli způsobem. Je to role klientské aplikace pro vykreslení image.

Pokud chcete přidat obsah pomocí direktivy Update nebo nahradit rozhraní API znalostní báze a obsah/soubor obsahuje značky HTML, můžete zachovat kód HTML v souboru tím, že zajistíte, aby se otevírání a zavírání značek převedlo v kódovaném formátu.

| Zachovat HTML  | Reprezentace v žádosti rozhraní API  | Reprezentace v KB |
|-----------|---------|-------------------------|
| Yes | \&lt; br \& gt; | &lt;BR&gt; |
| Yes | \&lt; H3 \& gt; Header \& lt;/H3 \& gt; | &lt;H3 – &gt; záhlaví &lt; /H3&gt; |

Znaky CR (\r\n) se navíc v KB převádějí na \n. LF (\n) se udržuje tak, jak je. V případě, že chcete řídicí sekvenci řídicích znaků, jako je například \t nebo \n, můžete použít zpětné lomítko, například: \\ \\ r \\ \\ n a \\ \\ t.

## <a name="next-steps"></a>Další kroky

Zkontrolujte [formáty souborů](reference-tsv-format-batch-testing.md)dávkového testování.