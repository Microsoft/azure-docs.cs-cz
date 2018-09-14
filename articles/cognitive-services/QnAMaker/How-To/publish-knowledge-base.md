---
title: Publikování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: 0a43c54db7af61b72a8e8e6188fd13ad1159271e
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45540904"
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
