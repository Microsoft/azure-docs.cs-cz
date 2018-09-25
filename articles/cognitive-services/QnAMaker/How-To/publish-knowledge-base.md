---
title: Publikování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: eceb0554d35935f1aee77c4c054cb7e65b327845
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033409"
---
# <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. 

Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.

![Publikování index prod testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

1. Po dokončení změn v znalostní BÁZÍ, vyberte **publikovat** v horním navigačním panelu. Až do přiděleného počtu znalostních bází můžete publikovat pro službu Azure Search. 

    ![Publikování znalostní báze](../media/qnamaker-how-to-publish-kb/publish.png)

2. Vyberte **publikovat** znovu zobrazíte podrobnosti o koncovém bodu, které lze použít v aplikaci nebo robotovi kódu.

    ![Publikování znalostní báze](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat analýzy na znalostní báze](./get-analytics-knowledge-base.md)
