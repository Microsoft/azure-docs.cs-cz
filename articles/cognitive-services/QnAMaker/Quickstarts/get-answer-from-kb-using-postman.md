---
title: 'Rychlý start: Pomocí nástroje Postman se získat odpověď ze znalostní báze – QnA Maker'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start vás provede získat odpověď ze znalostní báze pomocí nástroje Postman.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 01/03/2019
ms.author: diberry
ms.openlocfilehash: e58fe33368e461009a3f9756236518e13c812ca2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217335"
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

Žlutý čísla na následujícím obrázku určují, které název/hodnota dvojice používat v následujících krocích.

[![Publikování výsledků](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png)](../media/qnamaker-quickstart-get-answer-with-postman/publish-settings.png#lightbox)

Ke generování odpovědi pomocí Postman, proveďte následující kroky:

1. Otevřete nástroj Postman. Pokud budete vyzváni k výběru stavebním blokem, vyberte **základní žádosti** stavebním blokem. Nastavte **název žádosti** jako `Generate QnA Maker answer`a **kolekce** jako `Generate QnA Maker answers`. Pokud nechcete uložit do kolekce, vyberte **zrušit** tlačítko.
1. V pracovním prostoru, vyberte metodu HTTP **příspěvek**.

    [![V nástroji Postman nastavte metodu POST](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png)](../media/qnamaker-quickstart-get-answer-with-postman/postman-select-post-method.png#lightbox)

1. Pro adresu URL zřetězíte hodnotu hostitele (č. 2 z bitové kopie) a hodnotu Post (č. 1 z image) Chcete-li vytvořit úplnou adresu URL. Kompletní příklad adresa URL vypadá jako: 

    `https://qnamaker-f0.azurewebsites.net/qnamaker/knowledgebases/e1115f8c-d01b-4698-a2ed-85b0dbf3348c/generateAnswer`

    [![V nástroji Postman nastavte úplnou adresu URL](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-method-and-url.png#lightbox)

1. Vyberte **záhlaví** pod poli Adresa URL a potom vyberte **hromadných úprav**. 

1. Zkopírujte do textového pole hlavičky (#3 a 4 # z image).

    [![V nástroji Postman by nastavit hlavičky](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-headers.png#lightbox)

1. Vyberte **tělo** kartu.
1. Vyberte **nezpracovaná** formátování a zadejte ve formátu JSON (č. 5 z image), který představuje dotaz.

    `{"question":"How do I programmatically update my Knowledge Base?"}`

    [![V nástroji Postman by tělo nastavit, že hodnota JSON](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png)](../media/qnamaker-quickstart-get-answer-with-postman/set-postman-body-json-value.png#lightbox)

1. Vyberte **odeslat** tlačítko.
1. Odpověď obsahuje odpověď spolu s dalšími informacemi, které mohou být důležité do klientské aplikace. 

    [![V nástroji Postman by tělo nastavit, že hodnota JSON](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png)](../media/qnamaker-quickstart-get-answer-with-postman/receive-postman-response.png#lightbox)

## <a name="use-staging-endpoint"></a>Použít pracovní koncový bod

Pokud chcete získat odpověď z pracovní koncového bodu, připojte k adrese URL se logický parametr querystring `isTest` s hodnotou `true`.

`?isTest=true`

## <a name="next-steps"></a>Další postup

Stránka publikovat také obsahuje informace, které [generovat odpověď](get-answer-from-kb-using-curl.md) pomocí cURL. 

> [!div class="nextstepaction"]
> [Metadata použít při generování odpověď.](../How-to/metadata-generateanswer-usage.md)
