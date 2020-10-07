---
title: 'Kurz: vytvoření robota s nejčastějšími dotazy pomocí Azure Bot Service'
description: V tomto kurzu vytvoříte bez časté otázky týkající se kódu QnA Maker a Azure Bot Service.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 08/31/2020
ms.openlocfilehash: ab78ac41be2f900d84e82c21e566f5250b2afd18
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777399"
---
# <a name="tutorial-create-an-faq-bot-with-azure-bot-service"></a>Kurz: vytvoření robota s nejčastějšími dotazy pomocí Azure Bot Service
Pomocí QnA Maker a Azure [bot Service](https://azure.microsoft.com/en-us/services/bot-service/) si na stránce s přehledem dotazů vytvořte roboty, které nemají žádný kód.

V tomto kurzu se naučíte:

<!-- green checkmark -->
> [!div class="checklist"]
> * Propojení QnA Maker znalostní báze s Azure Bot Service
> * Nasazení robota
> * Chat s robotem ve službě Web Chat
> * Vysvětlete robot v podporovaných kanálech

## <a name="create-and-publish-a-knowledge-base"></a>Vytvoření a publikování znalostní báze

Postupujte podle pokynů v [rychlém](../Quickstarts/create-publish-knowledge-base.md) startu a vytvořte znalostní bázi. Po úspěšném publikování znalostní báze se dostanete na stránku níže.

![Snímek obrazovky s úspěšným publikováním](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Vytvoření robota

Po publikování můžete vytvořit robota ze stránky **publikování** :

* Můžete rychle vytvořit několik robotyů, které ukazují na stejnou znalostní bázi pro různé oblasti nebo cenové tarify pro jednotlivé roboty.
* Pokud potřebujete jenom jednu robota pro znalostní bázi, použijte **zobrazení všechny roboty na odkaz Azure Portal** k zobrazení seznamu aktuálního roboty.

Když provedete změny ve znalostní bázi a znovu publikujete, nemusíte s robotem dělat další akce. Je už nakonfigurovaná pro práci se znalostní bázeí a funguje se všemi budoucími změnami ve znalostní bázi. Pokaždé, když publikujete znalostní bázi, jsou automaticky aktualizovány všechny roboty připojené k ní.

1. Na QnA Makerovém portálu na stránce **publikovat** vyberte **vytvořit robot**. Toto tlačítko se zobrazí až po publikování znalostní báze.

    ![Snímek obrazovky s vytvořením robota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče pro Azure Portal se stránkou pro vytvoření Azure Bot Service. Nakonfigurujte službu Azure bot Service. Robot a QnA Maker můžou sdílet plán služby Web App Service, ale nemůžou webovou aplikaci sdílet. To znamená, že **název aplikace** pro robot se musí lišit od názvu aplikace pro službu QnA maker.

    * **Postup**
        * Změnit popisovač robota – Pokud není jedinečný
        * Vyberte jazyk sady SDK. Po vytvoření robota si můžete kód stáhnout do svého místního vývojového prostředí a pokračovat v procesu vývoje.
    * **Chybný postup**
        * Při vytváření robota změňte následující nastavení v Azure Portal. Jsou předem vyplněné pro stávající znalostní bázi:
           * QnA ověřovací klíč
           * Plán a umístění služby App Service


1. Po vytvoření robota otevřete prostředek **služby bot** .
1. V části **Správa robota**vyberte **test na webu chat**.
1. Na příkazovém řádku chatu **Zadejte zprávu**:

    `Azure services?`

    Robotka chatu odpoví odpověď z vaší znalostní báze.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Zadejte uživatelský dotaz do testovacího webového chatu.":::
1. Vysvětlete bot v dalších [podporovaných kanálech](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels?view=azure-bot-service-4.0&preserve-view=true).
