---
title: Kurz – sestavení zabezpečené webové aplikace v Azure App Service | Azure
description: V tomto kurzu se naučíte, jak vytvořit webovou aplikaci pomocí Azure App Service, povolit ověřování, volat Azure Storage a volat Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905498"
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
