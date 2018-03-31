---
title: Postupy upgradu Azure Mobile Engagement Web SDK | Microsoft Docs
description: Nejnovější aktualizace a postupy pro Web SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a20529b4-ec8d-4503-8ae9-09b5f0846d5b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 06/07/2016
ms.author: piyushjo
ms.openlocfilehash: 42f78b5e6bfa028f3a798f6cb6c0dee3f17b3ebe
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-web-sdk-upgrade-procedures"></a>Postupy upgradu Azure Mobile Engagement Web SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Pokud jste již spojili dřívější verzi webové sady Azure Mobile Engagement SDK do své webové aplikace, je potřeba zvážit následující body při upgradu sady SDK.

Při přeskočení více verzí sady SDK Mobile Engagement webové budete možná muset udělat několik postupů během procesu upgradu. Například pokud provádíte migraci z 1.4.0 1.6.0, nejdřív postupujte podle pokynů k upgradu z 1.4.0 1.5.0. Potom postupujte podle pokynů k upgradu z 1.5.0 1.6.0.

Podle toho, která verze upgradu, nahraďte všechny předchozí verze souboru azure-engagement.js nejnovější verzi souboru.

## <a name="upgrade-from-121-to-200"></a>Upgrade z 1.2.1 na 2.0.0
Tato část popisuje, jak migrovat integrace sady Mobile Engagement webového SDK z Capptain služby, které nabízí Capptain SAS, do aplikace Azure Mobile Engagement. Pokud provádíte migraci ze starší verze, naleznete na webu Capptain nejprve migrovat do 1.2.1 a potom použijte následující postupy.

Tato verze sady SDK Mobile Engagement webové nepodporuje Samsung inteligentní TV, Opera TV, webOS nebo funkci Reach.

> [!IMPORTANT]
> Capptain a Azure Mobile Engagement nejsou stejnou službu. Následující postup označuje pouze to, jak migrovat klientské aplikace. Migrace webových Mobile Engagement SDK do aplikace nebude přenést data ze serveru Capptain na serveru Mobile Engagement.
> 
> 

### <a name="javascript-files"></a>Soubory jazyka JavaScript
Nahraďte soubor capptain-sdk.js souboru azure-engagement.js a aktualizujte vaše skriptu importy odpovídajícím způsobem.

### <a name="remove-capptain-reach"></a>Odebrat Capptain Reach
Tato verze sady SDK Mobile Engagement webové nepodporuje funkci Reach. Pokud jste Capptain Reach integrovaná do aplikace, budete muset odebrat.

Odebrání stránku import dosáhnout šablon stylů CSS a odstraňte soubor .css související (capptain-reach.css, ve výchozím nastavení).

Odstraňte následující prostředky Reach: zavřít bitové kopie (capptain-close.png, ve výchozím nastavení) a na ikonu značky (capptain ikonu oznámení, ve výchozím nastavení).

Odeberte dosáhnout uživatelského rozhraní pro oznámení v aplikaci. Výchozí rozložení vypadá takto:

    <!-- capptain notification -->
    <div id="capptain_notification_area" class="capptain_category_default">
      <div class="icon">
        <img src="capptain-notification-icon.png" alt="icon" />
      </div>
      <div class="content">
        <div class="title" id="capptain_notification_title"></div>
        <div class="message" id="capptain_notification_message"></div>
      </div>
      <div id="capptain_notification_image"></div>
      <div>
        <button id="capptain_notification_close">Close</button>
      </div>
    </div>

Odeberte dosáhnout uživatelského rozhraní pro text a webovou oznámení a hlasování. Výchozí rozložení vypadá takto:

    <div id="capptain_overlay" class="capptain_category_default">
      <button id="capptain_overlay_close">x</button>
      <div id="capptain_overlay_title"></div>
      <div id="capptain_overlay_body"></div>
      <div id="capptain_overlay_poll"></div>
      <div id="capptain_overlay_buttons">
        <button id="capptain_overlay_exit"></button>
        <button id="capptain_overlay_action"></button>
      </div>
    </div>

Odeberte `reach` objektu z vaší konfigurace, pokud existuje. Vypadá takto:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Odeberte jakékoli jiné Reach přizpůsobení, jako např.

### <a name="remove-deprecated-apis"></a>Odeberte nepoužívané rozhraní API
Některé rozhraní API z Capptain jsou zastaralé v sadě SDK webové Mobile Engagement.

Odeberte všechny volání pro následující rozhraní API: `agent.connect`, `agent.disconnect`, `agent.pause`, a `agent.sendMessageToDevice`.

Odeberte všechny instance následující zpětná volání z konfiguraci Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, a `onPushMessageReceived`.

### <a name="configuration"></a>Konfigurace
Mobile Engagement používá připojovací řetězec konfigurace identifikátory SDK, například identifikátor aplikace.

ID aplikace nahraďte připojovací řetězec. Všimněte si, že objekt globální konfigurace sady SDK se změní z `capptain` k `azureEngagement`.

Před migrací:

    window.capptain = {
      appId: ...,
      [...]
    };

Po dokončení migrace:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      [...]
    };

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

### <a name="javascript-apis"></a>Rozhraní API jazyka JavaScript
Globální objekt jazyka JavaScript `window.capptain` byla přejmenována `window.azureEngagement` ale můžete použít `window.engagement` alias pro volání rozhraní API. Alias nelze použít k definování konfigurace sady SDK.

Například `capptain.deviceId` stane `engagement.deviceId`, `capptain.agent.startActivity` stane `engagement.agent.startActivity`a tak dále.

