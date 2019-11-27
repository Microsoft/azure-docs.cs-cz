---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 0b442c8cccf8ee9ed5194a7d3dfbfb4d7aa055a3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424416"
---
Tento rychlý Start založený na držiteli vás provede tím, že získáte odpověď ze znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Nejnovější [**příspěvek**](https://www.getpostman.com/).
* Musíte mít [QnA maker službu](../How-To/set-up-qnamaker-service-azure.md) a mít [znalostní bázi s dotazy a odpověďmi](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publikování pro získání koncového bodu

Až budete připraveni vygenerovat odpověď na otázku ze znalostní báze Knowledge Base, [publikujte](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) znalostní bázi.

## <a name="use-production-endpoint-with-postman"></a>Použití produkčního koncového bodu s předzálohovacím

Když je vaše znalostní báze publikována, zobrazí se na stránce **publikovat** nastavení požadavku HTTP, které vygeneruje odpověď. Výchozí zobrazení zobrazuje nastavení požadovaná pro vygenerování odpovědi od [post](https://www.getpostman.com).

Žluté číslice na následujícím obrázku označují dvojice název/hodnota, které se mají použít v následujících krocích.

[![publikování výsledků](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Pokud chcete vygenerovat odpověď s použitím metody post, proveďte následující kroky:

1. Otevřete post. Pokud jste požádáni o výběr stavebního bloku, vyberte základní stavební blok **požadavků** . Nastavte **název požadavku** jako `Generate QnA Maker answer`a **kolekci** jako `Generate QnA Maker answers`. Pokud nechcete ukládat do kolekce, vyberte tlačítko **Storno** .
1. V pracovním prostoru vyberte metodu HTTP **post**.

    [![v příspěvku, nastavit metodu POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. V poli Adresa URL zřetězí hodnotu hostitele (#2 z obrázku) a hodnotu post (#1 z obrázku), aby se vytvořila úplná adresa URL. Kompletní příklad adresy URL vypadá takto: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![v poli post, nastavte úplnou adresu URL.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. V části Adresa URL vyberte kartu **hlavičky** a pak vyberte **Hromadná úprava**. 

1. Zkopírujte hlavičky (#3 a #4 z obrázku) do textové oblasti.

    [![v poli post, nastavte záhlaví.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Vyberte kartu **tělo** .
1. Vyberte **nezpracovaný** formát a zadejte JSON (#5 z obrázku), který představuje otázku.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![v poli post, nastavte hodnotu JSON body.](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Klikněte na tlačítko **Odeslat** .
1. Odpověď obsahuje odpověď spolu s dalšími informacemi, které mohou být pro klientskou aplikaci důležité. 

    [![v poli post, nastavte hodnotu JSON body.](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Použít pracovní koncový bod

Pokud chcete získat odpověď z pracovního koncového bodu, přidejte adresu URL s vlastností text `isTest`.
