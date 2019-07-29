---
title: Vytvoření nové aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvářet a spravovat vaše aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7f9809fde088a03d4b20b5c739253f446c7a84b6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563620"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Vytvoření nové aplikace LUIS na portálu LUIS
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

## <a name="next-steps"></a>Další postup

Vaše první úkol v aplikaci je [přidání záměrů](luis-how-to-add-intents.md).
