---
title: 'Rychlý Start: Přidání otázek a odpovědí na portálu QnA Maker'
description: V tomto rychlém startu se dozvíte, jak přidat páry dotazů a odpovědí k metadatům, aby vaši uživatelé mohli najít správnou odpověď na jejich otázku.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: 930acbd3bbdb8f63b6aa888b292025a76435b289
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776744"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Rychlý Start: Přidání otázek a odpovědí pomocí portálu QnA Maker

Po vytvoření znalostní báze přidejte páry otázek a odpovědí (QnA) s metadaty k filtrování odpovědi. Otázky v následující tabulce jsou o omezeních služeb Azure, ale každá z nich musí mít jinou službu Azure Search.

<a name="qna-table"></a>

|Spojení|Dotazy|Odpověď|Metadata|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Po přidání metadat do páru QnA může klientská aplikace:

* Vyžádá odpovědi, které odpovídají jenom určitým metadatům.
* Příjem všech odpovědí, ale následné zpracování odpovědí v závislosti na metadatech pro každou odpověď.


## <a name="prerequisites"></a>Předpoklady

* Dokončení [předchozího rychlého](./create-publish-knowledge-base.md) startu

## <a name="sign-in-to-the-qna-maker-portal"></a>Přihlášení k portálu QnA Maker

1. Přihlaste se k [portálu QnA maker](https://www.qnamaker.ai).

1. Z [předchozího rychlého](../how-to/create-knowledge-base.md)startu vyberte svou stávající znalostní bázi.

## <a name="add-additional-alternatively-phrased-questions"></a>Přidat další otázky s alternativou fráze

Aktuální znalostní báze má QnA Maker řešení potíží s páry QnA. Tyto páry byly vytvořeny při přidání adresy URL do znalostní báze během procesu vytváření.

Při importu této adresy URL byla vytvořena pouze jedna otázka s jednou odpovědí. V tomto postupu přidejte další otázky.

1. Na stránce **Upravit** použijte vyhledávací pole nad páry otázka a odpověď, abyste zjistili otázku. `How large a knowledge base can I create?`

1. Ve sloupci **otázka** vyberte **+ Přidat alternativní formulování** a potom přidejte každou novou formulaci, která je uvedená v následující tabulce.

    |Alternativní formulace|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. Vyberte **Uložit a naučit** se znovu naučit znalostní bázi.

1. Vyberte **test**a pak zadejte otázku, která je blízko jedné z nových alternativních frází, ale není úplně stejná jako při zadávání slov:

    `What GB size can a knowledge base be?`

    Ve formátu Markdownu se vrátí správná odpověď:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Pokud vyberete v části vrácená odpověď možnost **zkontrolovat** , zobrazí se vám další odpovědi, ale ne se stejnou vysokou úrovní spolehlivosti.

    Nepřidávat všechny možné kombinace alternativní formulace. Když zapnete [aktivní výuku](../how-to/improve-knowledge-base.md)QnA maker, najde to alternativní formulování, které budou nejlépe pomáhat vaší znalostní bázi, aby splňovaly potřeby vašich uživatelů.

1. Vyberte znovu **test** pro zavření testovacího okna.

## <a name="add-metadata-to-filter-the-answers"></a>Přidat metadata pro filtrování odpovědí

Přidání metadat do dvojice otázek a odpovědí umožňuje klientské aplikaci požádat o filtrované odpovědi. Tento filtr se použije dřív, než se použije [první a druhý rozsah](../concepts/query-knowledge-base.md#ranker-process) .

1. Přidejte druhou dvojici otázek a odpovědí bez metadat z [první tabulky v tomto rychlém](#qna-table)startu a potom pokračujte následujícími kroky.

1. Vyberte **Možnosti zobrazení**a pak vyberte **zobrazit metadata**.

1. Pro pár QnA, který jste právě přidali, vyberte **Přidat značky metadat**a pak přidejte název `service` a hodnotu `search` . Vypadá to takto: `service:search` .

1. Přidejte další značku metadat s názvem `link_in_answer` a hodnotou `false` . Vypadá to takto: `link_in_answer:false` .

1. Vyhledá první odpověď v tabulce `How large a knowledge base can I create?` .

1. Přidejte páry metadat pro stejné dvě Tagy metadat:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Teď máte dvě otázky se stejnými tagy metadat s různými hodnotami.

1. Vyberte **Uložit a naučit** se znovu naučit znalostní bázi.

1. V horní nabídce vyberte **publikovat** a přejděte na stránku publikování.
1. Kliknutím na tlačítko **publikovat** publikujte aktuální znalostní bázi do koncového bodu.
1. Po publikování znalostní báze přejděte k dalšímu rychlému startu, kde se dozvíte, jak vygenerovat odpověď ze znalostní báze.

## <a name="what-did-you-accomplish"></a>Co jste provedli?

V rámci vaší znalostní báze jste upravili více otázek a zadávaných párů název/hodnota pro podporu filtrování při hledání horní odpovědi nebo postprocessing po vrácení odpovědi nebo odpovědi.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat dalším rychlým startem, odstraňte prostředky QnA Maker a robot Framework v Azure Portal.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání odpovědí s využitím nástroje Postman nebo cURL](get-answer-from-knowledge-base-using-url-tool.md)
