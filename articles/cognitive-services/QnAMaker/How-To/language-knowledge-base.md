---
title: Jiné než anglické znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker podporuje obsah znalostní báze v řadě jazyků. Každá služba QnA Maker však by měl být vyhrazen pro jeden jazyk. První znalostní báze vytvořen cílení na konkrétní služba QnA Maker nastaví jazyk této služby.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.openlocfilehash: e785abe09bba83f4ee69e7fd676422e22b44b391
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54261736"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Podpora jazyků znalostní báze obsahu pro nástroj QnA Maker
Nástroj QnA Maker podporuje obsah znalostní báze v řadě jazyků. Každá služba QnA Maker však by měl být vyhrazen pro jeden jazyk. První znalostní báze vytvořen cílení na konkrétní služba QnA Maker nastaví jazyk této služby. Zobrazit [tady](../Overview/languages-supported.md) pro seznam podporovaných jazyků.

Jazyk je automaticky rozpoznán obsah ze zdrojů dat extrahují. Jakmile vytvoříte novou službu QnA Maker a nové Knowledge Base dané služby, můžete ověřit, že jazyk byla nastavena správně.

1. Přejděte [webu Azure Portal](https://portal.azure.com/).

2. Vyberte **skupiny prostředků** a přejděte do skupiny prostředků, ve kterém je služba QnA Maker nasazené a vyberte **Azure Search** prostředků.

    ![Vyberte prostředek Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Vyberte **testkb** indexu. Tento index Azure Search je vždy první z nich vytvoří a obsahuje uložený obsah znalostních bází v dané služby. 

    ![Vyberte Test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Vyberte **pole** části zobrazující podrobnosti testkb.

    ![Vybrat pole](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Zaškrtněte políčko u **analyzátor** zobrazíte podrobností o jazycích.

    ![Vyberte analyzátor](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Měli byste najít, zda analyzátor nastaven na konkrétní jazyk. Tento jazyk byla automaticky zjištěna během kroku vytvoření znalostní báze knowledge base. Tento jazyk nelze změnit po vytvoření prostředku.

    ![Vybrané analyzátoru](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytváření robotů QnA pomocí služby Azure Bot Service](../Tutorials/create-qna-bot.md)
