---
title: 'Úvodní příručka: Přidání otázek a odpovědí na portálu QnA Maker'
description: Tento rychlý start ukazuje, jak přidat sady otázek a odpovědí s metadaty, aby uživatelé mohli najít správnou odpověď na svou otázku.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 25c0fe549dfc850a53b06f79f348a87cba3b70a1
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109927"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Úvodní příručka: Přidávání otázek a odpovědí pomocí portálu QnA Maker

Po vytvoření znalostní báze přidejte sady otázek a odpovědí (QnA) s metadaty pro filtrování odpovědi. Otázky v následující tabulce se o omezení služeb Azure, ale každý má co do činění s jinou službou Azure.

<a name="qna-table"></a>

|Nastavit|Dotazy|Odpověď|Metadata|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Jakmile jsou metadata přidána do sady QnA, klientská aplikace může:

* Vyžádejte si odpovědi, které odpovídají pouze určitým metadatům.
* Přijímat všechny odpovědi, ale po zpracování odpovědi v závislosti na metadata pro každou odpověď.


## <a name="prerequisites"></a>Požadavky

* Dokončení [předchozího rychlého startu](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>Přihlášení k portálu QnA Maker

1. Přihlaste se k [portálu QnA Maker](https://www.qnamaker.ai).

1. Vyberte existující znalostní bázi z [předchozího rychlého startu](../how-to/create-knowledge-base.md).

## <a name="add-additional-alternatively-phrased-questions"></a>Přidání dalších alternativních otázek

Aktuální znalostní báze obsahuje sady QnA Maker pro řešení potíží s qnA. Tyto sady byly vytvořeny při přidání adresy URL do znalostní báze během procesu vytváření.

Při importu této adresy URL byla vytvořena pouze jedna otázka s jednou odpovědí. V tomto postupu přidejte další otázky.

1. Na stránce **Úpravy** použijte textové pole hledání nad sadami otázek a odpovědí, abyste našli otázku`How large a knowledge base can I create?`

1. Ve sloupci **Otázka** vyberte **+ Přidat alternativní formulace a** pak přidejte každou novou frázi uvedenou v následující tabulce.

    |Alternativní formulace|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Chcete-li přeškolit znalostní bázi, vyberte **možnost Uložit a trénovat.**

1. Vyberte **Testovat**a zadejte otázku, která se blíží jedné z nových alternativních formulací, ale není úplně stejná:

    `What GB size can a knowledge base be?`

    Správná odpověď je vrácena ve formátu markdown:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Pokud vyberete **Zkontrolovat** pod vrácenou odpovědí, můžete zobrazit další odpovědi splněny otázku, ale ne se stejnou vysokou úrovní spolehlivosti.

    Nepřidávejte všechny možné kombinace alternativních frázování. Když zapnete [aktivní učení](../how-to/improve-knowledge-base.md)qnA makeru , najde se tak alternativní formulace, které nejlépe pomohou vaší znalostní bázi vyhovět potřebám vašich uživatelů.

1. **Chcete-li** testovací okno zavřít, vyberte znovu testovat.

## <a name="add-metadata-to-filter-the-answers"></a>Přidání metadat pro filtrování odpovědí

Přidání metadat do sady otázek a odpovědí umožňuje klientské aplikaci požadovat filtrované odpovědi. Tento filtr se použije před použitím [prvního a druhého rankers.](../concepts/query-knowledge-base.md#ranker-process)

1. Přidejte druhou sadu otázek a odpovědí bez metadat z [první tabulky v tomto rychlém startu](#qna-table)a pokračujte následujícími kroky.

1. Vyberte **Zobrazit volby**, pak vyberte **Zobrazit metadata**.

1. Pro sadu QnA, kterou jste právě přidali, vyberte `service` **Přidat značky metadat**a pak přidejte název a hodnotu . `search` Vypadá to takto: `service:search`.

1. Přidejte další značku metadat `link_in_answer` s `false`názvem a hodnotou . Vypadá to takto: `link_in_answer:false`.

1. Vyhledejte první odpověď v `How large a knowledge base can I create?`tabulce .

1. Přidejte dvojice metadat pro stejné dvě značky metadat:

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    Nyní máte dvě otázky se stejnými značkami metadat s různými hodnotami.

1. Chcete-li přeškolit znalostní bázi, vyberte **možnost Uložit a trénovat.**

1. Vyberte **Publikovat** v horní nabídce a přejděte na stránku publikování.
1. Vyberte tlačítko **Publikovat,** chcete-li publikovat aktuální znalostní bázi do koncového bodu.
1. Po publikování znalostní báze pokračujte dalším rychlým startem a zjistěte, jak generovat odpověď ze znalostní báze.

## <a name="what-did-you-accomplish"></a>Čeho jsi dosáhl?

Upravili jste znalostní bázi, abyste podpořili další otázky, a zadali jste dvojice název/hodnota, které podporují filtrování během hledání horní odpovědi nebo postprocessingu, po vrácení odpovědi nebo odpovědí.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepokračujete na další rychlý start, odstraňte prostředky architektury QnA Maker a Bot na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání odpovědí s využitím nástroje Postman nebo cURL](get-answer-from-knowledge-base-using-url-tool.md)