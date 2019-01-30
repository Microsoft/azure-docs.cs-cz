---
title: Publikování znalostní báze
titleSuffix: QnA Maker - Azure Cognitive Services
description: Publikování znalostní báze je posledním krokem při zpřístupnění znalostní bázi jako koncový bod odpovídání na otázku. Při publikování znalostní báze QnA obsah znalostní báze přesune z indexu testovacího do produkčního prostředí indexu ve službě Azure search.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/11/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 2db8a87f28905ab03fb45d56943ae40d01908527
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225376"
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
