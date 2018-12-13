---
title: 'Rychlý start: Pomocí nástroje Postman se získat odpověď ze znalostní báze – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start vás provede získat odpověď ze znalostní báze pomocí nástroje Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: diberry
ms.openlocfilehash: 476e982bdddd41c1daf06c3a673d964ce2a85f98
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270888"
---
# <a name="quickstart-get-an-answer-from-knowledge-base-using-postman"></a>Rychlý start: Získat odpověď ze znalostní báze pomocí nástroje Postman

Tento rychlý start na základě Postman provede získat odpověď ze znalostní báze.

## <a name="prerequisites"></a>Požadavky

* Nejnovější [ **Postman**](https://www.getpostman.com/).
* Musíte mít [služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md) a mít [znalostní báze knowledge base s otázkami a odpověďmi](../Tutorials/create-publish-query-in-portal.md). 

## <a name="publish-to-get-endpoint"></a>Publikování na získání koncového bodu

Až budete připravení ke generování odpověď na dotaz zadaný ze znalostní báze [publikovat](../How-to/publish-knowledge-base.md) znalostní báze.

## <a name="use-production-endpoint-with-postman"></a>Použití produkční koncový bod pomocí nástroje Postman

Při publikování znalostní báze **publikovat** zobrazí se stránka nastavení požadavku HTTP ke generování odpovědi. Výchozí zobrazení ukazuje nastavení vyžadovaných ke generování odpověď na základě [Postman](https://www.getpostman.com).

[![Publikování výsledků](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Ke generování odpovědi pomocí Postman, proveďte následující kroky:

1. Otevřete nástroj Postman. 
1. Vyberte stavebním blokem, chcete-li vytvořit základní požadavek.
1. Nastavte **název žádosti** jako `Generate QnA Maker answer`a **kolekce** jako `Generate QnA Maker answers`.
1. V pracovním prostoru, vyberte metodu HTTP **příspěvek**.
1. Pro adresu URL zřetězíte hodnotu hostitele a hodnotu Post vytvořit úplnou adresu URL. 

    [![V nástroji Postman nastavte jako metodu Post a úplnou adresu URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Vyberte **záhlaví** pod poli Adresa URL a potom vyberte **hromadných úprav**. 
1. Zkopírujte záhlaví do textového pole.

    [![V nástroji Postman by nastavit hlavičky](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Vyberte **tělo** kartu.
1. Vyberte **nezpracovaná** formátování a zadejte ve formátu JSON, který představuje dotaz.

    [![V nástroji Postman by tělo nastavit, že hodnota JSON](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Vyberte **odeslat** tlačítko.
1. Odpověď obsahuje odpověď spolu s dalšími informacemi, které mohou být důležité do klientské aplikace. 

    [![V nástroji Postman by tělo nastavit, že hodnota JSON](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint-with-curl"></a>Použít pracovní koncový bod pomocí cURL

Pokud chcete získat odpověď z pracovní koncového bodu, použijte parametr logické hodnoty řetězce dotazu `isTest` s hodnotou `true`.

`isTest=true`

## <a name="next-steps"></a>Další postup

Stránka publikovat také obsahuje informace, které [generovat odpověď](get-answer-from-kb-using-curl.md) pomocí cURL. 

> [!div class="nextstepaction"]
> [Metadata použít při generování odpověď.](../How-to/metadata-generateanswer-usage.md)