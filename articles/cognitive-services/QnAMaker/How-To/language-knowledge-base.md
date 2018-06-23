---
title: Postup vytvoření jiných než anglických znalostní báze knowledge base - QnA Maker - Azure kognitivní Services | Microsoft Docs
description: Postup vytvoření jiných než anglických znalostní bázi knowledge base.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: 3fbd590229044af0daa60968fd8d556d539a58c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343254"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze knowledge base QnA Maker
QnA Maker podporuje obsah znalostní báze knowledge base v mnoha jazycích. Každá služba QnA Maker však by mělo být vyhrazeno pro jeden jazyk. První znalostní báze vytvořen cílení na konkrétní službu QnA Maker nastaví jazyk této služby. V tématu [sem](../Overview/languages-supported.md) pro seznam podporovaných jazyků.

Jazyk je automaticky rozpozná z obsahu zdroje dat se extrahují. Jakmile vytvoříte novou službu Maker QnA a nové báze Knowledge Base v této službě, můžete ověřit správně nastavené jazyk.

1. Přejděte na [portál Azure](https://portal.azure.com/).

2. Vyberte **skupiny prostředků** a přejděte do skupiny prostředků, kde je služba QnA Maker nasazené a vyberte **Azure Search** prostředků.

    ![Vyberte prostředek Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Vyberte **testkb** index. Tento index Azure Search je vždy první z nich vytvořit a obsahuje uložený obsah znalostních bází v dané služby. 

    ![Vyberte testovací KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. Vyberte **pole** část s podrobnostmi testkb.

    ![Vybrat pole](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Zaškrtněte políčko pro **analyzátor** zobrazíte podrobnosti jazyk.

    ![Vyberte analyzátor](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. By měl zjistit, zda Analyzátor je nastavena na konkrétního jazyka. Tento jazyk byla automaticky zjištěna během kroku vytvoření znalostní báze knowledge base. Tento jazyk nelze změnit po vytvoření prostředku.

    ![Vybrané analyzátor](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvořit QnA robota robota službou Azure](../Tutorials/create-qna-bot.md)
