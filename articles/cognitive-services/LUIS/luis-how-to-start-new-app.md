---
title: Vytvoření nové aplikace
titleSuffix: Language Understanding - Azure Cognitive Services
description: Vytvářet a spravovat vaše aplikace na webové stránce Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: daffbe3f3158bb232f7db7ac90d766661e937643
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60197484"
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

## <a name="import-an-app-from-file"></a>Import aplikace ze souboru

1. Na **Moje aplikace** stránce **novou aplikaci Import**.
1. V místním dialogovém okně vyberte soubor JSON platný aplikace a pak vyberte **provádí**.

### <a name="import-errors"></a>Chyby importu

Možné chyby jsou: 

* Aplikace s tímto názvem už existuje. Opětovné importování aplikace a nastavení **volitelný název** na nový název. 

## <a name="export-app-for-backup"></a>Exportovat aplikaci pro zálohování

1. Na **Moje aplikace** stránce **exportovat**.
1. Vyberte **exportovat jako JSON**. Prohlížeč stáhne aktivní verzi aplikace.
1. Přidejte tento soubor do zálohování systému pro archivaci modelu.

## <a name="export-app-for-containers"></a>Exportovat aplikaci pro kontejnery

1. Na **Moje aplikace** stránce **exportovat**.
1. Vyberte **exportovat jako kontejner** vyberte, které publikované slotu (produkční nebo fázi), kterou chcete exportovat.
1. Použít tento soubor s vaší [LUIS kontejneru](luis-container-howto.md). 

    Pokud vás zajímají export trénovaného ale ne, ale publikované model se má použít s kontejnerem služby LUIS, pokračujte **verze** stránku a z něj exportovat. 

## <a name="delete-app"></a>Odstranit aplikaci

1. Na **Moje aplikace** vyberte tři tečky (...) na konci řádku aplikace.
1. Vyberte **odstranit** z nabídky.
1. Vyberte **Ok** v potvrzovacím okně.

## <a name="next-steps"></a>Další postup

Vaše první úkol v aplikaci je [přidání záměrů](luis-how-to-add-intents.md).
