---
title: Kurz – sestavení zabezpečené webové aplikace v Azure App Service | Azure
description: V tomto kurzu se naučíte, jak vytvořit webovou aplikaci pomocí Azure App Service, povolit ověřování, volat Azure Storage a volat Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 5b94000ce59b3a115e53ecdcd0849b97aae552c5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221836"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Kurz: povolení ověřování v App Service a přístup k úložišti a Microsoft Graph

Tento kurz popisuje běžný scénář použití aplikace, ve kterém se naučíte:

- [Nakonfigurujte ověřování pro webovou aplikaci](scenario-secure-app-authentication-app-service.md) a omezte přístup uživatelům ve vaší organizaci. Viz v diagramu.
- [Zabezpečený přístup k Azure Storage](scenario-secure-app-access-storage.md) pro webovou aplikaci pomocí spravovaných identit. Viz B v diagramu.
- Přístup k datům v Microsoft Graph [pro přihlášeného uživatele](scenario-secure-app-access-microsoft-graph-as-user.md) nebo [pro webovou aplikaci](scenario-secure-app-access-microsoft-graph-as-app.md) pomocí spravovaných identit. Viz C v diagramu.
- [Vyčistěte prostředky](scenario-secure-app-clean-up-resources.md) , které jste pro tento kurz vytvořili.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagram znázorňující scénáře aplikací v platformě Microsoft identity" border="false":::

Začněte tím, že se dozvíte, jak povolit ověřování pro webovou aplikaci.

> [!div class="nextstepaction"]
> [Konfigurace ověřování pro webovou aplikaci](scenario-secure-app-authentication-app-service.md)
