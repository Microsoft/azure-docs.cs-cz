---
title: 'Rychlý Start: publikování znalostní báze, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý Start založený na REST Java publikuje vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo v robotovi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: diberry
ms.openlocfilehash: 44b53cbfdb1982d9f9e6a0cb6408a16b1d660d2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447422"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Javy

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá Apache [klienta HTTP](https://hc.apache.org/httpcomponents-client-ga/) ze součástí HTTP. Je potřeba přidat následující knihovny klienta Apache HTTP do projektu:
    * httpclient 4.5.3.jar
    * httpcore 4.4.6.jar
    * Commons. protokolování 1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .
* V adrese URL v parametru řetězce dotazu `kbid` QnA Maker ID znalostní báze (KB), jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-Java** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Vytvořte soubor Java

Otevřete VSCode a vytvořte nový soubor s názvem `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části `PublishKB.java`, nad třídy, přidejte následující řádky do potřebné závislosti přidejte do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Vytvořte třídu PublishKB s main – metoda

Po závislostech přidejte následující třídu:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

V **hlavní** metodu, přidejte požadované konstanty na používání nástroje QnA Maker. Nahraďte hodnoty vlastními.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidání požadavku POST k publikování znalostní báze

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavit a spustit program z příkazového řádku. Automaticky se odešle požadavek rozhraní API nástroje QnA Maker a vytiskne se do okna konzoly.

1. Sestavení souboru:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Spusťte soubor:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze, je nutné [adresu URL koncového bodu pro generování odpovědi](../Tutorials/create-publish-answer.md#generating-an-answer).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
