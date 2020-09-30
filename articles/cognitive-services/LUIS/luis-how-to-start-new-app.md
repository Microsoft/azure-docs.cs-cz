---
title: Vytvoření nové aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvářejte a spravujte své aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: fcc803e87b2efd3e94b90eb17258ab3bb156359f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541424"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Vytvoření nové aplikace LUIS na portálu LUIS
Existuje několik způsobů, jak vytvořit aplikaci LUIS. Aplikaci LUIS můžete vytvořit na portálu LUIS nebo prostřednictvím [rozhraní API](developer-reference-resource.md)pro vytváření Luis.

## <a name="using-the-luis-portal"></a>Používání portálu LUIS

Novou aplikaci můžete vytvořit na portálu několika způsoby:

* Začněte s prázdnou aplikací a vytvořte záměry, projevy a entity.
* Začněte s prázdnou aplikací a přidejte [předem vytvořenou doménu](luis-how-to-use-prebuilt-domains.md).
* Importujte aplikaci LUIS ze `.lu` `.json` souboru nebo, který už obsahuje záměry, projevy a entity.

## <a name="using-the-authoring-apis"></a>Použití rozhraní API pro vytváření obsahu
Novou aplikaci s rozhraními API pro vytváření obsahu můžete vytvořit několika způsoby:

* [Přidat aplikaci](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – začněte s prázdnou aplikací a vytvořte záměry, projevy a entity.
* [Přidejte předem vytvořenou aplikaci](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) – začněte s předem vytvořenou doménou, včetně záměrů, projevyů a entit.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Vytvoření nové aplikace v LUIS

1. Na stránce **Moje aplikace** vyberte své **předplatné**a vytvořte  **prostředek** a potom **+ vytvořit**. 

> [!div class="mx-imgBorder"]
> ![Seznam aplikací LUIS](./media/create-app-in-portal.png)

1. V dialogovém okně zadejte název vaší aplikace, například `Pizza Tutorial` .

    ![Dialogové okno vytvořit novou aplikaci](./media/create-pizza-tutorial-app-in-portal.png)

1. Zvolte jazykovou verzi vaší aplikace a potom vyberte **Hotovo**. Popis a předpověď prostředku jsou v tuto chvíli volitelné. V části **Spravovat** portálu můžete kdykoli nastavit.

    > [!NOTE]
    > Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi.

    Po vytvoření aplikace se na portálu LUIS zobrazí seznam **záměrů** s `None` již vytvořeným záměrem. Teď máte prázdnou aplikaci.

    > [!div class="mx-imgBorder"]
    > ![Seznam záměrů s žádnými záměry vytvořenými bez příkladu projevy.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>Další akce, které jsou k dispozici na stránce Moje aplikace

Panel nástrojů kontextu poskytuje další akce:

* Přejmenovat aplikaci
* Import ze souboru pomocí `.lu` nebo `.json`
* Exportovat aplikaci jako `.lu` (pro [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json` nebo `.zip` (pro [kontejner Luis](luis-container-howto.md))
* Umožňuje importovat protokoly koncových bodů kontejneru a zkontrolovat projevy koncového bodu.
* Export protokolů koncového bodu, jako je `.csv` , pro offline analýzu
* Odstranit aplikaci

## <a name="next-steps"></a>Další kroky

Pokud návrh aplikace zahrnuje detekci záměrů, [vytvořte nové záměry](luis-how-to-add-intents.md)a přidejte příklad projevy. Pokud je návrh aplikace pouze extrahování dat, přidejte příklad projevy k záměru None, pak [vytvořte entity](luis-how-to-add-example-utterances.md)a označte příklad projevy s těmito entitami.
