---
title: 'Rychlý Start: Přidání otázek a odpovědí na portálu QnA Maker'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: ed50e6adbcca7cbb4935400c7850c37dc2ed389f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803533"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Rychlý Start: Přidání otázek a odpovědí pomocí portálu QnA Maker

Po vytvoření znalostní báze přidejte sadu dotazů a odpovědí s metadaty, aby vaši uživatelé mohli najít správnou odpověď na jejich otázku.

Správná odpověď je jediná odpověď, ale existuje mnoho způsobů, jak může zákazník požádat o dotaz, který vede k této jediné odpovědi.

Například otázky v následující tabulce jsou o omezeních služeb Azure, ale každá z nich musí mít jinou službu Azure. 

<a name="qna-table"></a>


|Sada|Otázky|Odpověď|Metadata|
|--|--|--|--|
|1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Po přidání metadat do sady otázek a odpovědí může klientská aplikace:

* Vyžádá odpovědi, které odpovídají jenom určitým metadatům.
* Příjem všech odpovědí, ale následné zpracování odpovědí v závislosti na metadatech pro každou odpověď.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="prerequisites"></a>Předpoklady

* Služba QnA Maker
* Znalostní báze vytvořený v této QnA Maker službě

V [prvním rychlém](../how-to/create-knowledge-base.md)startu byly vytvořeny obě.

## <a name="sign-in-to-the-qna-maker-portal"></a>Přihlášení k portálu QnA Maker

1. Přihlaste se k [portálu QnA maker](https://www.qnamaker.ai).

1. Vyberte stávající znalostní bázi. Pokud nemáte znalostní bázi Knowledge Base, vraťte se k [předchozímu rychlému](../how-to/create-knowledge-base.md) startu a dokončete kroky k vytvoření znalostní báze.

## <a name="add-additional-alternatively-phrased-questions"></a>Přidat další otázky s alternativou fráze 

Aktuální znalostní báze od [předchozího rychlého](../how-to/create-knowledge-base.md)startu má QnA maker otázka a sady odpovědí pro řešení potíží. Tyto sady byly vytvořeny při přidání adresy URL do znalostní báze během procesu vytváření. 

Při importu této adresy URL byla vytvořena pouze jedna otázka s jednou odpovědí. 

V tomto postupu přidejte další otázky.

1. Na stránce **Upravit** použijte vyhledávací pole nad rámec otázek a odpovědí a vyhledejte otázku `How large a knowledge base can I create?`.

1. Ve sloupci **otázka** vyberte **+ Přidat alternativní formulování** a potom přidejte každou novou formulaci, která je uvedená v následující tabulce.

    |Alternativní formulace|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. Vyberte **Uložit a naučit** se znovu naučit znalostní bázi. 

1. Vyberte **test**a pak zadejte otázku, která je blízko jedné z nových alternativních frází, ale není úplně stejná jako při zadávání slov:

    `What GB size can a knowledge base be?`

    Ve formátu Markdownu se vrátí správná odpověď: `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Pokud vyberete v části vrácená odpověď možnost **zkontrolovat** , zobrazí se vám další odpovědi, ale ne se stejnou vysokou úrovní spolehlivosti. 

    Nepřidávat všechny možné kombinace alternativní formulace. Zapněte si [aktivní výuku](../how-to/improve-knowledge-base.md)QnA maker, vyhledáme alternativní formulování, které budou nejlépe pomáhat vaší znalostní bázi, aby splňovali potřeby vašich uživatelů.

1. Vyberte znovu **test** pro zavření testovacího okna.

## <a name="add-metadata-to-filter-the-answers"></a>Přidat metadata pro filtrování odpovědí

Přidání metadat do otázky a sady odpovědí umožňuje klientské aplikaci požádat o filtrované odpovědi. Tento filtr se použije dřív, než se použije [první a druhý rozsah](../concepts/knowledge-base.md#ranker-process) .

1. Přidejte druhou otázku a sadu odpovědí, bez metadat, z [první tabulky v tomto rychlém](#qna-table)startu, a pak pokračujte následujícími kroky. 

1. Vyberte **Možnosti zobrazení**a pak vyberte **zobrazit metadata**. 

1. Pro otázku a sadu odpovědí, které jste právě přidali, vyberte **Přidat značky metadat**a pak přidejte název `service` a hodnotu `search` `service:search`.

1. Přidejte další značky metadat s názvem `link_in_answer` a hodnotou `false` `link_in_answer:false`.

1. Vyhledejte první odpověď v tabulce `How large a knowledge base can I create?`. 
1. Přidejte páry metadat pro stejné dvě Tagy metadat:

    `link_in_answer`: `true`<br>
    `server`: `qna_maker`

    Teď máte dvě otázky se stejnými tagy metadat s různými hodnotami. 

1. Vyberte **Uložit a naučit** se znovu naučit znalostní bázi. 

1. V horní nabídce vyberte **publikovat** a přejděte na stránku publikování. 
1. Kliknutím na tlačítko **publikovat** publikujte aktuální znalostní bázi do koncového bodu Queryable. 
1. Po publikování znalostní báze vyberte kartu **kudrlinkou** , abyste zobrazili příklad složeného příkazu, který slouží k vygenerování odpovědi ze znalostní báze.
1. Zkopírujte příkaz do panelu poznámek nebo jiného upravitelného prostředí, abyste mohli příkaz Upravit. Upravte vlastní název prostředku, ID znalostní báze a klíč koncového bodu:

    |Nahradit|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Všimněte si, že otázka je pouze jedno slovo, `size`, což může vracet buď otázku a sadu odpovědí. Pole `strictFilters` instruuje odpověď, aby se snížila pouze na odpovědi `qna_maker`. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. Odpověď obsahuje pouze odpověď, která splňuje kritéria filtru. 

    Následující odpověď na kudrlinkou byla naformátována pro čitelnost:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Pokud je k dispozici sada otázek a odpovědí, která nevyhověla hledanému termínu, ale nesplnila tento filtr, nebude vrácena. Místo toho se vrátí obecná odpověď @no__t – 0.

    Zajistěte, aby páry názvů a hodnot metadat byly v rámci požadovaných limitů. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získat odpověď s použitím post](get-answer-from-kb-using-postman.md)