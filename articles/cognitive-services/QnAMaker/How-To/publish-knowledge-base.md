---
title: Publikování znalostní báze
titleSuffix: QnA Maker API - Azure Cognitive Services
description: Publikování znalostní báze se službou API nástroje QnA Maker se poslední krok zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/11/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: c65654e7d6b2e66a07116ea9555ed316ace88912
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091949"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Publikování znalostní báze na portálu služby API nástroje QnA Maker

Publikování znalostní báze se službou API nástroje QnA Maker se poslední krok zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. 

Při publikování znalostní bázi otázek a odpovědí obsah znalostní báze přesune z index testu do produkčního prostředí indexu ve službě Azure search.

![Publikování index prod testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

1. Po dokončení změn v znalostní BÁZÍ, vyberte **publikovat** v horním navigačním panelu. Až do přiděleného počtu znalostních bází můžete publikovat pro službu Azure Search. 

    ![Publikování znalostní báze](../media/qnamaker-how-to-publish-kb/publish.png)

2. Vyberte **publikovat** znovu zobrazíte podrobnosti o koncovém bodu, které lze použít v aplikaci nebo robotovi kódu.

    ![Publikování proběhlo úspěšně znalostní báze](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s znalostní báze, odeberte ho na portálu pro nástroj QnA Maker.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat analýzy na znalostní báze](./get-analytics-knowledge-base.md)
