---
title: Postup publikování znalostní báze – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Postup publikování znalostní bázi knowledge base
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e9dbeacfb0df98c6b8f084c263690c05fe966cdc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343222"
---
# <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

Publikování znalostní báze je v posledním kroku zpřístupnění znalostní báze jako koncový bod odpovědi na otázky. 

Při publikování znalostní bázi knowledge base QnA obsah znalostní báze přesune z indexu test na produkční index ve službě Azure search.

![Publikování index produkčnímu testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

## <a name="publish-a-knowledge-base"></a>Publikování znalostní báze

1. Po dokončení změn ve vašem KB, vyberte **publikovat** v horním navigačním panelu. Do přiděleného počtu znalostních bází, které můžete publikovat pro službu Azure Search. 

    ![Publikování znalostní báze knowledge base](../media/qnamaker-how-to-publish-kb/publish.png)

2. Vyberte **publikovat** znovu a zobrazit podrobnosti koncový bod, které lze použít v kódu aplikace nebo robota.

    ![Publikování znalostní báze knowledge base](../media/qnamaker-how-to-publish-kb/publish-success.png)
    
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získat analytics na znalostní báze](./get-analytics-knowledge-base.md)
