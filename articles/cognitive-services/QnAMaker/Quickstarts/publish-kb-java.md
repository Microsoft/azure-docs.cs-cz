---
title: 'Úvodní příručka: Publikování znalostní báze REST, Java - QnA Maker'
description: Tento rychlý start založený na jazyce Java REST publikuje znalostní bázi a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotovi.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 149d7963f29bf041cda75fffaac533e0a62ee7a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851682"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Rychlý start: Publikování znalostní báze ve službě QnA Maker pomocí Javy

Tento rychlý start založený na rest vás provede programovým publikováním znalostní báze (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Cognitive Search a vytvoří koncový bod, který lze volat ve vaší aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá [klienta Apache HTTP](https://hc.apache.org/httpcomponents-client-ga/) z http komponent. Do projektu je třeba přidat následující klientské knihovny Apache HTTP:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Kód visual studia](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete načíst klíč a koncový bod (který obsahuje název prostředku), vyberte **Rychlý start** pro váš prostředek na webu Azure Portal.
* ID znalostní báze QnA Maker (KB) nalezené v adrese URL v parametru řetězce dotazu, `kbid` jak je znázorněno níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud znalostní bázi ještě nemáte, můžete si vytvořit ukázkovou znalostní bázi a použít ji v tomto rychlém startu. Přečtěte si o [vytvoření nové znalostní báze](create-new-kb-csharp.md).

> [!NOTE]
> Kompletní soubor řešení jsou k dispozici v [ **azure-samples/cognitive-services-qnamaker-java** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Vytvoření souboru java

Otevřete VSCode a vytvořte nový soubor s názvem `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

V horní `PublishKB.java`části položky nad třídou přidejte následující řádky, které přidají do projektu nezbytné závislosti:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Vytvořit třídu PublishKB s hlavní metodou

Za závislosti přidejte následující třídu:

```Go
public class PublishKB {

    public static void main(String[] args)
    {
    }
}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

V **hlavní** metodě přidejte požadované konstanty pro přístup k QnA Maker. Nahraďte hodnoty vlastními.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Přidat požadavek POST pro publikování znalostní báze

Po požadovaných konstantách přidejte následující kód, který do rozhraní API qnA makeru vytvoří požadavek HTTPS, aby publikoval znalostní bázi a obdrží odpověď:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Vytvořte a spusťte program z příkazového řádku. Automaticky odešle požadavek do rozhraní QnA Maker API a poté se vytiskne do okna konzoly.

1. Vytvořte soubor:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Spusťte soubor:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

Po publikování znalostní báze potřebujete [adresu URL koncového bodu ke generování odpovědi](./get-answer-from-knowledge-base-java.md).

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)