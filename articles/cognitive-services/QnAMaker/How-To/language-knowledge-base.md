---
title: Znalostní báze jiné než anglické verze – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První znalostní báze vytvořila cílení na konkrétní službu QnA Maker nastaví jazyk této služby.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: diberry
ms.openlocfilehash: 26792246267ced6d9fff50fe4fea11cc8d280d6a
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966673"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker
QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První znalostní báze vytvořila cílení na konkrétní službu QnA Maker nastaví jazyk této služby. Seznam podporovaných jazyků najdete [tady](../Overview/languages-supported.md) .

Jazyk je automaticky rozpoznán z obsahu extrahovaných zdrojů dat. Po vytvoření nové služby QnA Maker a nové znalostní báze v této službě můžete ověřit, že je jazyk nastavený správně.

1. Přejděte na [portál Azure Portal](https://portal.azure.com/).

2. Vyberte **skupiny prostředků** a přejděte do skupiny prostředků, ve které je nainstalovaná služba QnA maker, a vyberte prostředek **Azure Search** .

    ![Vybrat prostředek Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

3. Vyberte **Testkb** index. Tento index Azure Search je vždy první vytvořený a obsahuje uložený obsah všech znalostní báze v této službě. 

    ![Vyberte test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

4. V části Výběr **polí** se zobrazí podrobnosti Testkb.

    ![Vybrat pole](../media/qnamaker-how-to-language-kb/selectfields.png)

5. Pokud chcete zobrazit podrobnosti o jazyku, zaškrtněte políčko u analyzátoru.

    ![Vybrat analyzátor](../media/qnamaker-how-to-language-kb/select-analyzer.png)

6. Měli byste zjistit, že je analyzátor nastavený na určitý jazyk. Tento jazyk byl automaticky zjištěn během kroku vytvoření znalostní báze. Po vytvoření prostředku nelze tento jazyk změnit.

    ![Vybraný analyzátor](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření QnA robota pomocí Azure Bot Service](../Tutorials/create-qna-bot.md)
