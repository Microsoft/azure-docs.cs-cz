---
title: Vytvoření nové aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvářejte a spravujte své aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500313"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Vytvoření nové aplikace LUIS na portálu LUIS
Existuje několik způsobů, jak vytvořit aplikaci LUIS. Aplikaci LUIS můžete vytvořit na portálu [Luis](https://www.luis.ai) nebo prostřednictvím [rozhraní API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)pro vytváření Luis.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>Používání portálu LUIS

Novou aplikaci můžete na portálu LUIS vytvořit několika způsoby:

* Začněte s prázdnou aplikací a vytvořte záměry, projevy a entity.
* Začněte s prázdnou aplikací a přidejte [předem vytvořenou doménu](luis-how-to-use-prebuilt-domains.md).
* Importujte aplikaci LUIS ze souboru JSON, který už obsahuje záměry, projevy a entity.

## <a name="using-the-authoring-apis"></a>Použití rozhraní API pro vytváření obsahu
Novou aplikaci s rozhraními API pro vytváření obsahu můžete vytvořit několika způsoby:

* [Začněte](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) s prázdnou aplikací a vytvořte záměry, projevy a entity.
* [Začněte](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) předem vytvořenou doménou.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Vytvoření nové aplikace v LUIS

1. Na stránce **Moje aplikace** vyberte **+ vytvořit**.

    ![Seznam aplikací LUIS](./media/luis-create-new-app/apps-list.png)


2. Do dialogového okna zadejte název aplikace "TravelAgent".

    ![Dialogové okno vytvořit novou aplikaci](./media/luis-create-new-app/create-app.png)

3. Zvolte jazykovou verzi vaší aplikace (pro aplikaci TravelAgent zvolte angličtinu) a potom vyberte **Hotovo**. 

    > [!NOTE]
    > Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

## <a name="import-an-app-from-file"></a>Import aplikace ze souboru

1. Na stránce **Moje aplikace** vyberte **importovat novou aplikaci**.
1. V místním dialogovém okně Vyberte platný soubor JSON aplikace a potom vyberte **Hotovo**.

### <a name="import-errors"></a>Chyby importu

Možné chyby: 

* Aplikace s tímto názvem již existuje. Pokud to chcete opravit, znovu naimportujte aplikaci a nastavte **volitelný název** na nový název. 

## <a name="export-app-for-backup"></a>Exportovat aplikaci pro zálohování

1. Na stránce **Moje aplikace** vyberte **exportovat**.
1. Vyberte **exportovat jako JSON**. Prohlížeč stáhne aktivní verzi aplikace.
1. Přidejte tento soubor do zálohovacího systému, abyste mohli model archivovat.

## <a name="export-app-for-containers"></a>Exportovat aplikaci pro kontejnery

1. Na stránce **Moje aplikace** vyberte **exportovat**.
1. Vyberte **exportovat jako kontejner** a potom vyberte, která publikovaná pozice (výroba nebo fáze) chcete exportovat.
1. Tento soubor použijte s [kontejnerem Luis](luis-container-howto.md). 

    Pokud vás zajímá export výukového, ale dosud nepublikovaného modelu, který se má použít s kontejnerem LUIS, klikněte na stránku **verze** a exportujte si ho z tohoto webu. 

## <a name="delete-app"></a>Odstranit aplikaci

1. Na stránce **Moje aplikace** vyberte tři tečky (...) na konci řádku aplikace.
1. V nabídce vyberte **Odstranit** .
1. V potvrzovacím okně vyberte **OK** .

## <a name="next-steps"></a>Další kroky

Prvním úkolem v aplikaci je [Přidat záměry](luis-how-to-add-intents.md).
