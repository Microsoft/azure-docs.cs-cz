---
title: Obsah Windows Universal SDK aplikace
description: Další informace o obsahu sady Windows SDK pro univerzální aplikace pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8fa1b701-1c2b-4aec-940c-06c974ef5405
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 7b520adcc6af24f7b092580ea82a787a120668bf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-content"></a>Obsah Windows Universal SDK aplikace
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento dokument uvádí a popisuje obsah nasadit pomocí sady SDK v aplikaci.

## <a name="the-resources-folder"></a>`/Resources` Složky
Tato složka obsahuje všechny prostředky, které potřebuje Mobile Engagement. Můžete také upravit, aby vyhovovaly vaší aplikace.

* `EngagementConfiguration.xml` Konfigurační soubor: Mobile Engagement, to je, kde můžete upravit nastavení Mobile Engagementu (Mobile Engagement připojovací řetězec, sestava havárií...).

### <a name="html-folder"></a>Složka/HTML
* `EngagementNotification.html` : `Notification` Webové zobrazení html návrh hlaviček v aplikaci.
* `EngagementAnnouncement.html` : `Announcement` Webové zobrazení html návrhu pro vkládaná zobrazení v aplikaci.

### <a name="images-folder"></a>/Images složky
* `EngagementIconNotification.png` Ikona: brand zobrazí vlevo od oznámení, nahraďte tento vaší brand ikonou.
* `EngagementIconOk.png` : `Ok` Ikona reach stránky obsahu pro tlačítko akce nebo ověření.
* `EngagementIconNOK.png` : `NOK` Ikony používané při ověřování tlačítko stránky obsahu reach je zakázané.
* `EngagementIconClose.png` : `Close` Ikona reach oznámení a obsah pro tlačítko Zavřít.

### <a name="overlay-folder"></a>/Overlay složky
* `EngagementPageOverlay.cs` : Překrytí stránce zodpovědný za přidání zapojení dosáhnout uživatelského rozhraní v aplikaci na jeho podřízené.

