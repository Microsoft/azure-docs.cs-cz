---
title: Vytvoření nové aplikace – LUIS
titleSuffix: Azure Cognitive Services
description: Vytvořte a spravujte své aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220828"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Vytvoření nové aplikace LUIS na portálu LUIS
Existuje několik způsobů, jak vytvořit aplikaci LUIS. Aplikaci LUIS můžete vytvořit na portálu LUIS nebo prostřednictvím vytváření [webových api](developer-reference-resource.md)LUIS .

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>Použití portálu LUIS

Novou aplikaci můžete na portálu preview vytvořit několika způsoby:

* Začněte s prázdnou aplikací a vytvořte záměry, projevy a entity.
* Začněte s prázdnou aplikací a přidejte [předem vypranou doménu](luis-how-to-use-prebuilt-domains.md).
* Importujte aplikaci `.lu` LUIS `.json` z nebo souboru, který již obsahuje záměry, projevy a entity.

## <a name="using-the-authoring-apis"></a>Použití vytváření api
Můžete vytvořit novou aplikaci s vytvářením API několika způsoby:

* [Přidejte aplikaci](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – začněte s prázdnou aplikací a vytvořte záměry, projevy a entity.
* [Přidejte předem vypranou aplikaci](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) – začněte s předem vypranou doménou, včetně záměrů, projevy a entity.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Vytvoření nové aplikace ve službě LUIS

1. Na stránce **Moje aplikace** vyberte předplatné a zdroj pro vytváření pak **+ Vytvořit**. Pokud používáte bezplatný zkušební klíč, přečtěte si, jak [vytvořit zdroj pro vytváření](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![Seznam aplikací LUIS](./media/create-app-in-portal.png)


1. V dialogovém okně zadejte název aplikace, `Pizza Tutorial`například .

    ![Dialogové okno Vytvořit novou aplikaci](./media/create-pizza-tutorial-app-in-portal.png)

1. Zvolte jazykovou verzi aplikace a pak vyberte **Hotovo**. Popis a předpověď prostředku jsou volitelné v tomto okamžiku. Potom můžete nastavit kdykoli v sekci **Správa** na portálu.

    > [!NOTE]
    > Jakmile se aplikace vytvoří, nemůžete změnit její jazykovou verzi. 

    Po vytvoření aplikace se na portálu LUIS zobrazí `None` seznam záměry se **záměrem,** který je pro vás již vytvořen. Nyní máte prázdnou aplikaci. 
    
    > [!div class="mx-imgBorder"]
    > ![Záměry seznam s žádný záměr vytvořen bez příkladu projevy.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Další dostupné akce

Kontextový panel nástrojů obsahuje další akce:

* Přejmenování aplikace
* Import ze `.lu` souboru pomocí nebo`.json`
* Exportovat `.lu` aplikaci jako `.json`(pro `.zip` [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), nebo (pro [kontejner LUIS)](luis-container-howto.md)
* Importovat protokoly koncových bodů kontejneru, chcete-li zkontrolovat projevy koncového bodu
* Export protokolů koncových `.csv`bodů pro offline analýzu
* Odstranit aplikaci

## <a name="next-steps"></a>Další kroky

Pokud návrh aplikace zahrnuje zjišťování [záměru, vytvořte nové záměry](luis-how-to-add-intents.md)a přidejte ukázkové projevy. Pokud je návrh aplikace jenom extrakce dat, přidejte ukázkové projevy do záměru None, pak [vytvořte entity](luis-how-to-add-example-utterances.md)a označte ukázkové projevy s těmito entitami. 
