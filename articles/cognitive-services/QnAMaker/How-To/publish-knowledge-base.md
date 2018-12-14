---
title: Publikování znalostní báze
titleSuffix: QnA Maker - Azure Cognitive Services
description: Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: deeebd99425bc1ec1ed2ae76ef4852119a9c10ea
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342438"
---
# <a name="publish-a-knowledge-base-using-the-qna-maker-portal"></a>Publikování znalostní báze na portálu pro nástroj QnA Maker

Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku do klientské aplikace. 

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
