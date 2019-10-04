---
title: 'Rychlý Start: publikování znalostní báze, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý Start založený na REST Java vás provede publikováním znalostní báze, který odešle nejnovější verzi testované znalostní báze do vyhrazeného Azure Search indexu představujícího publikovanou znalostní bázi. Vytvoří také koncový bod, který lze volat ve vaší aplikaci nebo robotovi chatu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 4ee622c944c5ccd39331ab395eca7b6ff9692b35
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836069"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý Start: publikování znalostní báze v QnA Maker pomocí jazyka Java

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného Azure Search indexu a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi chatu.

Tento rychlý Start volá QnA Maker rozhraní API:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) – toto rozhraní API nevyžaduje žádné informace v těle žádosti.

## <a name="prerequisites"></a>Požadavky

* [JDK se](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá klienta Apache [http](https://hc.apache.org/httpcomponents-client-ga/) ze součástí http. Do projektu musíte přidat tyto klientské knihovny Apache HTTP: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [QnA maker službu](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .
* V adrese URL v parametru řetězce dotazu kbid se našla ID znalostní báze (KB), jak je znázorněno níže. QnA Maker

    ![ID znalostní báze QnA Maker Knowledge Base](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud ještě nemáte znalostní bázi Knowledge Base, můžete si vytvořit ukázku pro použití v tomto rychlém startu: [Vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-Java** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Vytvoření souboru Java

Otevřete VSCode a vytvořte nový soubor s názvem `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Přidat požadované závislosti

V horní části `PublishKB.java` nad třídou přidejte následující řádky pro přidání nezbytných závislostí do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Vytvořit třídu PublishKB s metodou Main

Po závislostech přidejte následující třídu:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Přidat požadované konstanty

V metodě **Main** přidejte požadované konstanty pro přístup k QnA maker. Nahraďte hodnoty vlastními.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Volání rozhraní API vrátí stav 204 pro úspěšné publikování bez jakéhokoli obsahu v těle odpovědi. Kód přidá obsah pro odpovědi 204.

U jakékoli jiné odpovědi se tato odpověď vrátí beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Sestavte a spusťte program z příkazového řádku. Požadavek bude automaticky odesílat do rozhraní API služby QnA Maker a pak bude vytištěn v okně konzoly.

1. Sestavte soubor:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Spusťte soubor:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Referenční informace pro QnA Maker (v4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
