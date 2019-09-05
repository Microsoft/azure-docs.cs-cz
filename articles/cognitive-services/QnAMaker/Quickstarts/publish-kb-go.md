---
title: 'Rychlý start: Publikování znalostní báze, REST, přejít-QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu založené na protokolu REST přejít provede publikování znalostní báze, která odesílá na nejnovější verzi znalostní báze otestované vyhrazené index Azure Search představující publikované znalostní báze. Také se přitom vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: a1e53b21a3168e9745d36e191cc86ba9d20192b5
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70308147"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>Rychlý start: Publikování znalostní báze ve QnA Maker pomocí jazyka přejít

V tomto rychlém startu založené na protokolu REST provede programově publikování znalostní bázi (KB). Publikování odešle nejnovější verzi znalostní báze do vyhrazeného indexu Azure Search a vytvoří koncový bod, který je možné volat v aplikaci nebo chatovacím robotu.

Tento rychlý start volá rozhraní API služby QnA Maker:
* [Publikování](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) –toto rozhraní API nevyžaduje v těle požadavku žádné informace.

## <a name="prerequisites"></a>Požadavky

* [Go 1.10.1](https://golang.org/dl/)
* Potřebujete [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete získat klíč, vyberte na řídicím panelu **Klíče** v části **Správa prostředků**. 

* ID znalostní báze služby QnA Maker, které najdete v adrese URL v parametru kbid řetězce dotazu, jak vidíte níže.

    ![ID znalostní báze ve službě QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Pokud ještě nemáte znalostní bázi Knowledge Base, můžete si vytvořit ukázku pro použití v tomto rychlém startu: [Vytvořte novou znalostní bázi](create-new-kb-csharp.md).

> [!NOTE] 
> Kompletní soubory řešení jsou k dispozici v [úložišti GitHub **Azure-Samples/vnímání-Services-qnamakerem-přejít** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-go-file"></a>Vytvořte soubor Go

Otevřete VSCode a vytvořte nový soubor s názvem `publish-kb.go`.

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

Na začátek souboru `publish-kb.go` přidejte následující řádky k přidání potřebných závislostí do projektu:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=3-7 "Add the required dependencies")]

## <a name="create-the-main-function"></a>Vytvoření funkce main

Po požadované závislosti přidejte následující třídu:

```Go
package main

func main() {

}
```

## <a name="add-required-constants"></a>Přidání požadovaných konstant

Uvnitř **hlavní**


 Funkce, přidejte požadované konstanty na používání nástroje QnA Maker. Nahraďte hodnoty vlastními.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/publish-knowledge-base/publish-kb.go?range=16-20 "Add the required constants")]

## <a name="add-post-request-to-publish-kb"></a>Přidejte požadavek POST pro publikování znalostní báze

Za požadované konstanty přidejte následující kód, který odešle požadavek HTTPS API nástroje QnA Maker na publikování znalostní báze a přijímat odpovědi:

[!code-go[Add a POST request to publish KB](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to publish KB")]

Volání API vrátí v případě úspěšného publikování stav 204, přičemž tělo odpovědi bude prázdné. Kód v případě odpovědi se stavem 204 přidá obsah.

V případě jakékoli jiné odpověď se vrátí tato odpověď beze změny.

## <a name="build-and-run-the-program"></a>Sestavení a spuštění programu

Zadáním následujícího příkazu soubor zkompilujte. Příkazový řádek nevrací žádné informace o úspěšném sestavení.

```bash
go build publish-kb.go
```

Spusťte program zadáním následujícího příkazu na příkazovém řádku. Žádost se odešle do API nástroje QnA Maker publikovat KB a pak vytiskněte 204 úspěchu nebo chyby.

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Další postup

Po publikování znalostní báze, je nutné [adresu URL koncového bodu pro generování odpovědi](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Reference k rozhraní REST API služby QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)
