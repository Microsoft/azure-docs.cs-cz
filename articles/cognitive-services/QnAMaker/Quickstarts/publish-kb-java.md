---
title: 'Rychlý Start: publikování znalostní báze, REST, Java-QnA Maker'
description: Tento rychlý Start založený na REST Java publikuje vaši znalostní bázi a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo v robotovi.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27, devx-track-java
ms.topic: how-to
ms.openlocfilehash: 34184123a3fc096c014dc0185b51b088c59a6e04
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777506"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý Start založený na REST vás provede programově publikováním znalostní báze (KB). Publikování nabídne nejnovější verzi znalostní báze do vyhrazeného indexu služby Azure Kognitivní hledání a vytvoří koncový bod, který se dá volat ve vaší aplikaci nebo robotovi pro chat.

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
* V adrese URL parametru řetězce dotazu bylo nalezeno QnA Maker znalostní báze (KB) `kbid` , jak je uvedeno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-Java** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Vytvoření souboru Java

Otevřete VSCode a vytvořte nový soubor s názvem `PublishKB.java` .

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní části `PublishKB.java` nad třídou přidejte následující řádky pro přidání nezbytných závislostí do projektu:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="dependencies":::

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

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="constants":::

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který vytvoří požadavek HTTPS rozhraní API služby QnA Maker k publikování znalostní báze a obdrží odpověď:

:::code language="java" source="~/cognitive-services-quickstart-code/java/QnAMaker/rest/PublishKB.java" id="post":::

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

Po publikování znalostní báze budete potřebovat [adresu URL koncového bodu, aby se vygenerovala odpověď](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
