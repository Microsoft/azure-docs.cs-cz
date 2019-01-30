---
title: Vytvoření nové aplikace
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vytvářet a spravovat vaše aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 3c35bb96c3ba5dbf1c3302836b2c73cf15128937
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215168"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Vytvoření nové aplikace LUIS na portálu služby LUIS
Existuje několik způsobů, jak vytvořit aplikaci LUIS. Můžete vytvořit aplikaci LUIS v [LUIS](https://www.luis.ai) portálu, nebo prostřednictvím LUIS vytváření [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>Na portálu služby LUIS
Vytvoření nové aplikace na portálu služby LUIS několika způsoby:

* Začít s prázdnou aplikaci a vytvořit záměrů, projevy a entity.
* Začít s prázdnou aplikaci a přidejte [předem připravených domény](luis-how-to-use-prebuilt-domains.md).
* Importujte aplikace LUIS ze souboru JSON, který již obsahuje záměrů, projevy a entity.

## <a name="using-the-authoring-apis"></a>Pomocí rozhraní API pro vytváření obsahu
Vytvořit novou aplikaci s rozhraním API pro vytváření obsahu v několika způsoby:

* [Spustit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) s prázdnou aplikaci a vytvořit záměrů, projevy a entity.
* [Spustit](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) s využitím předem připravených doménou.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Vytvoření nové aplikace v LUIS

1. Na **Moje aplikace** stránce **vytvořit novou aplikaci**.

    ![Seznam aplikací LUIS](./media/luis-create-new-app/apps-list.png)


2. V dialogovém okně pojmenujte svoji aplikaci "TravelAgent".

    ![Vytvoření nového dialogového okna aplikace](./media/luis-create-new-app/create-app.png)

3. Zvolte jazykovou verzi vaší aplikace (TravelAgent aplikaci, zvolte možnost angličtina) a pak vyberte **provádí**. 

    > [!NOTE]
    > Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 


## <a name="next-steps"></a>Další postup

Vaše první úkol v aplikaci je [přidání záměrů](luis-how-to-add-intents.md).
