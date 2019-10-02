---
title: 'Rychlý Start: publikování znalostní báze, REST, Java-QnA Maker'
titleSuffix: Azure Cognitive Services
description: Tento rychlý Start založený na REST Java vás provede publikováním znalostní báze, který odešle nejnovější verzi testované znalostní báze do vyhrazeného Azure Search indexu představujícího publikovanou znalostní bázi. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 5b97081cad352a6f6dcbf92b879584e3efa8f4ff
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802876"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Předpoklady

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá klienta Apache [http](https://hc.apache.org/httpcomponents-client-ga/) ze součástí http. Do projektu musíte přidat tyto klientské knihovny Apache HTTP: 
    * HttpClient-4.5.3. jar
    * httpcore-4.4.6. jar
    * Commons-Logging-1.2. jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst svůj klíč a koncový bod (včetně názvu prostředku), vyberte pro prostředek v Azure Portal **rychlý Start** .
* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-Java** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Vytvoření souboru Java

Otevřete VSCode a vytvořte nový soubor s názvem `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Přidejte požadované závislosti

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

## <a name="add-required-constants"></a>Přidání požadovaných konstant

V metodě **Main** přidejte požadované konstanty pro přístup k QnA maker. Nahraďte hodnoty vlastními.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

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
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
