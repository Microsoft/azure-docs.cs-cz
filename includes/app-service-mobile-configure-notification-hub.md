---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 0a74079677a084b2d4e8221cf5a74b356126811d
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42817454"
---
Funkce Mobile Apps služby Azure App Service používá [Azure Notification Hubs] k odesílání nabízených oznámení, tak se konfigurace centra oznámení pro mobilní aplikace.

1. V webu [Azure Portal], přejděte na stránku **App Services**a pak vyberte váš back-end aplikací. V části **nastavení**vyberte **Push**.
2. Chcete-li přidat prostředek centra oznámení do aplikace, **připojit**. Můžete buď vytvořit centrum nebo připojení k existující.

    ![Konfigurace rozbočovače](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teď jste připojení na centrum oznámení do projektu back-end Mobile Apps. Později nakonfigurujete toto centrum oznámení pro připojení k systému oznámení platformy (PNS) nainstalovat do zařízení.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
