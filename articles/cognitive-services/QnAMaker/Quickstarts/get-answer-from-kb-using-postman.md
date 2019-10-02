---
title: 'Rychlý Start: použití metody post pro získání odpovědi z znalostní báze Knowledge Base – QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak získat odpověď ze znalostní báze pomocí post.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/01/2019
ms.author: diberry
ms.openlocfilehash: 3df2703ba24091d0d21f06ae55aca389837e93d9
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803037"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Rychlý Start: získání odpovědi ze znalostní báze pomocí metody post

Tento rychlý Start založený na držiteli vás provede tím, že získáte odpověď ze znalostní báze.

## <a name="prerequisites"></a>Předpoklady

* Nejnovější [**příspěvek**](https://www.getpostman.com/).
* Musíte mít [QnA maker službu](../How-To/set-up-qnamaker-service-azure.md) a mít [znalostní bázi s dotazy a odpověďmi](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publikování pro získání koncového bodu

Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze Knowledge Base, [publikujte](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) znalostní bázi.

## <a name="use-production-endpoint-with-postman"></a>Použití produkčního koncového bodu s předzálohovacím

Když je vaše znalostní báze publikována, zobrazí se na stránce **publikovat** nastavení požadavku HTTP, které vygeneruje odpověď. Výchozí zobrazení zobrazuje nastavení požadovaná pro vygenerování odpovědi od [post](https://www.getpostman.com).

Žluté číslice na následujícím obrázku označují dvojice název/hodnota, které se mají použít v následujících krocích.

[výsledky @no__t – 1Publish](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Pokud chcete vygenerovat odpověď s použitím metody post, proveďte následující kroky:

1. Otevřete post. Pokud jste požádáni o výběr stavebního bloku, vyberte základní stavební blok **požadavků** . Jako **název žádosti** nastavte @no__t – 1and **kolekci** jako `Generate QnA Maker answers`. Pokud nechcete ukládat do kolekce, vyberte tlačítko **Storno** .
1. V pracovním prostoru vyberte metodu HTTP **post**.

    [@no__t – 1In po nastavení metody POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. V poli Adresa URL zřetězí hodnotu hostitele (#2 z obrázku) a hodnotu post (#1 z obrázku), aby se vytvořila úplná adresa URL. Kompletní příklad adresy URL vypadá takto: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![In, nastavte úplnou adresu URL.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. V části Adresa URL vyberte kartu **hlavičky** a pak vyberte **Hromadná úprava**. 

1. Zkopírujte hlavičky (#3 a #4 z obrázku) do textové oblasti.

    [@no__t – 1In, nastavení hlaviček](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Vyberte kartu **tělo** .
1. Vyberte **nezpracovaný** formát a zadejte JSON (#5 z obrázku), který představuje otázku.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [@no__t – 1In, nastavení hodnoty JSON textu](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Klikněte na tlačítko **Odeslat** .
1. Odpověď obsahuje odpověď spolu s dalšími informacemi, které mohou být pro klientskou aplikaci důležité. 

    [@no__t – 1In, nastavení hodnoty JSON textu](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Použít pracovní koncový bod

Pokud chcete získat odpověď z pracovního koncového bodu, přidejte adresu URL s vlastností těla `isTest`.

## <a name="next-steps"></a>Další kroky

Stránka publikování také poskytuje informace pro [vygenerování odpovědi](get-answer-from-kb-using-curl.md) s kudrlinkou. 

> [!div class="nextstepaction"]
> [Použití metadat při generování odpovědi](../How-to/metadata-generateanswer-usage.md)
