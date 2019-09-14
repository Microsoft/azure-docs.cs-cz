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
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961474"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker

QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První znalostní báze vytvořila cílení na konkrétní službu QnA Maker nastaví jazyk této služby. Seznam podporovaných jazyků najdete [tady](../Overview/languages-supported.md) .

Jazyk je automaticky rozpoznán z obsahu extrahovaných zdrojů dat. Po vytvoření nové služby QnA Maker a nové znalostní báze v této službě můžete ověřit, že je jazyk nastavený správně.

1. Přejděte na [Azure Portal](https://portal.azure.com/).

1. Vyberte **skupiny prostředků** a přejděte do skupiny prostředků, ve které je nainstalovaná služba QnA maker, a vyberte prostředek **Azure Search** .

    ![Vybrat prostředek Azure Search](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Vyberte **indexy**a pak vyberte index **Testkb** . Toto je první vytvořený index Azure Search a obsahuje uložený obsah všech základů znalostí v dané službě. 

1. Vyberte **pole** , chcete-li zobrazit pole v indexu.

1. `answer` Sloupec _analyzátoru_ pro pole ajenastavennakonkrétníjazyk.`questions` Tento jazyk byl automaticky zjištěn během kroku vytvoření znalostní báze z importovaných souborů a adres URL. Po vytvoření prostředku nelze tento jazyk změnit.

    ![Vybraný analyzátor](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření QnA robota pomocí Azure Bot Service](../Tutorials/create-qna-bot.md)
