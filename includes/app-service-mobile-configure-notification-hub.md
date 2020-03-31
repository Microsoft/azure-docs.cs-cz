---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175510"
---
Funkce Mobilní aplikace služby Azure App Service používá k odesílání nabízených oznámení [centra Azure Notification Hubs,] takže budete konfigurovat centrum oznámení pro vaši mobilní aplikaci.

1. Na [webu Azure Portal]přejděte na **App Services**a vyberte back-end aplikace. V části **Nastavení**vyberte **Push**.
2. Pokud chcete do aplikace přidat prostředek centra oznámení, vyberte **Připojit**. Můžete buď vytvořit rozbočovač, nebo se připojit k existujícímu rozbočovači.

    ![Konfigurace rozbočovače](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Nyní jste připojili centrum oznámení k back-endprojektu mobilních aplikací. Později nakonfigurujete tento rozbočovač oznámení pro připojení k systému oznámení platformy (PNS) pro nabízený do zařízení.

[Portál Azure]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
