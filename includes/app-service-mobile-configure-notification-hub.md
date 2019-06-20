---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: da3793c428c624ce3a224cbd7606ab26c4a50803
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175510"
---
Funkce Mobile Apps služby Azure App Service používá [Azure Notification Hubs] k odesílání nabízených oznámení, tak se konfigurace centra oznámení pro mobilní aplikace.

1. V [Azure Portal], přejděte na stránku **App Services**a pak vyberte váš back-end aplikací. V části **nastavení**vyberte **Push**.
2. Chcete-li přidat prostředek centra oznámení do aplikace, **připojit**. Můžete buď vytvořit centrum nebo připojení k existující.

    ![Konfigurace rozbočovače](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Teď jste připojení na centrum oznámení do projektu back-end Mobile Apps. Později nakonfigurujete toto centrum oznámení pro připojení k systému oznámení platformy (PNS) nainstalovat do zařízení.

[Azure Portal]: https://portal.azure.com/
[Azure Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-push-notification-overview/
