---
title: 'Úvodní příručka: Získejte odpověď ze znalostní báze - REST, Java - QnA Maker'
description: Tento rychlý start založený na jazyce Java REST vás provede získáním odpovědi z znalostní báze programově.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: conceptual
ms.openlocfilehash: 67f09b6d1e284cdf35825a2e584b372bd2adf70a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851737"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-java"></a>Úvodní příručka: Získejte odpovědi na otázku z databáze znalostí pomocí Javy

Tento rychlý start vás provede programově získáním odpovědi z publikované znalostní báze QnA Maker. Znalostní báze obsahuje otázky a odpovědi ze [zdrojů dat,](../Concepts/knowledge-base.md) jako jsou časté dotazy. [Otázka](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) je odeslána službě QnA Maker. [Odpověď](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) obsahuje nejlépe předpovídanou odpověď.

[Ukázka referenční dokumentace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [Sample](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/blob/master/documentation-samples/quickstarts/get-answer/GetAnswer.java)

## <a name="prerequisites"></a>Požadavky

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* Tato ukázka používá [klienta Apache HTTP](https://hc.apache.org/httpcomponents-client-ga/) z http komponent. Do projektu je třeba přidat následující klientské knihovny Apache HTTP:
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Kód visual studia](https://code.visualstudio.com/)
* Musíte mít [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete klíč načíst, vyberte **klíče** v části **Správa prostředků** na řídicím panelu Azure pro prostředek QnA Maker.
* **Publikovat** nastavení stránky. Pokud nemáte publikovanou znalostní bázi, vytvořte prázdnou znalostní bázi, importujte znalostní bázi na stránce **Nastavení** a poté ji publikujte. Tuto [základní znalostní bázi](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)si můžete stáhnout a použít .

    Nastavení stránky publikování zahrnuje hodnotu postupu POST, hodnotu hostitele a hodnotu EndpointKey.

    ![Publish settings (Nastavení publikování)](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-java-file"></a>Vytvoření souboru java

Otevřete VSCode a vytvořte nový soubor s názvem `GetAnswer.java` a přidejte následující třídu:

```Java
public class GetAnswer {

    public static void main(String[] args)
    {

    }
}
```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Tento rychlý start používá třídy Apache pro požadavky HTTP. Nad GetAnswer třídy v horní `GetAnswer.java` části souboru, přidejte potřebné závislosti do projektu:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=5-13 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Přidání požadovaných konstant

V horní části `GetAnswer.java` třídy přidejte požadované konstanty pro přístup k QnA Maker. Tyto hodnoty jsou na stránce **Publikovat** po publikování znalostní báze.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=26-42 "Add the required constants")]

## <a name="add-a-post-request-to-send-question"></a>Přidání požadavku POST k odeslání otázky

Následující kód provede požadavek HTTPS na rozhraní API qnA maker u odesílání otázky do znalostní báze a obdrží odpověď:

[!code-java[Add a POST request to send question to knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/get-answer/GetAnswer.java?range=44-72 "Add a POST request to send question to knowledge base")]

Hodnota `Authorization` záhlaví obsahuje řetězec `EndpointKey`.

Přečtěte si další informace o [žádosti](../how-to/metadata-generateanswer-usage.md#generateanswer-request) a [odpovědi](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Vytvořte a spusťte program z příkazového řádku. Automaticky odešle požadavek do rozhraní QnA Maker API a poté se vytiskne do okna konzoly.

1. Vytvořte soubor:

    ```bash
    javac -cp "lib/*" GetAnswer.java
    ```

1. Spusťte soubor:

    ```bash
    java -cp ".;lib/*" GetAnswer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)