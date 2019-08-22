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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876627"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker

QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První znalostní báze vytvořila cílení na konkrétní službu QnA Maker nastaví jazyk této služby. Seznam podporovaných jazyků najdete [tady](../Overview/languages-supported.md) .

Jazyk je automaticky rozpoznán z obsahu extrahovaných zdrojů dat. Po vytvoření nové služby QnA Maker a nové znalostní báze v této službě můžete ověřit, že je jazyk nastavený správně.

1. Přejděte na [Azure Portal](https://portal.azure.com/).

1. Vyberte **skupiny prostředků** a přejděte do skupiny prostředků, ve které je nainstalovaná služba QnA maker, a vyberte prostředek **Azure Search** .

    ![Vybrat prostředek Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Vyberte **Testkb** index. Tento index Azure Search je vždy první vytvořený a obsahuje uložený obsah všech znalostní báze v této službě. 

    ![Vyberte test KB](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. V části Výběr **polí** se zobrazí podrobnosti _Testkb_ .

    ![Vybrat pole](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Pokud chcete zobrazit podrobnosti o jazyku, zaškrtněte políčko u analyzátoru.

    ![Vybrat analyzátor](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Měli byste zjistit, že je _analyzátor_ nastavený na konkrétní jazyk. Tento jazyk byl automaticky zjištěn během kroku vytvoření znalostní báze z importovaných souborů a adres URL. Po vytvoření prostředku nelze tento jazyk změnit.

    ![Vybraný analyzátor](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření QnA robota pomocí Azure Bot Service](../Tutorials/create-qna-bot.md)
