---
title: Kurz – vytvoření zabezpečení webové aplikace v Azure App Service | Azure
description: V tomto kurzu se naučíte, jak vytvořit webovou aplikaci pomocí Azure App Service, povolit ověřování, zavolat službu Azure Storage a volat Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428847"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Kurz: povolení ověřování v App Service a přístup k úložišti a Microsoft Graph

Tento kurz popisuje běžný scénář použití aplikace a naučíte se:

- [(A) nakonfigurujte ověřování pro webovou aplikaci](scenario-secure-app-authentication-app-service.md) a omezte přístup uživatelům ve vaší organizaci.
- [(B) zabezpečený přístup k Azure Storage](scenario-secure-app-access-storage.md) jménem webové aplikace pomocí spravovaných identit.
- (C) přístup k datům v Microsoft Graph [jménem přihlášeného uživatele](scenario-secure-app-access-microsoft-graph-as-user.md) nebo [jménem webové aplikace](scenario-secure-app-access-microsoft-graph-as-app.md) pomocí spravovaných identit.
- [Vyčistěte prostředky](scenario-secure-app-clean-up-resources.md) , které jste pro tento kurz vytvořili.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Scénáře aplikací v platformě Microsoft Identity Platform" border="false":::

Začněte tím, že se dozvíte, jak povolit ověřování pro webovou aplikaci.

> [!div class="nextstepaction"]
> [Konfigurace ověřování pro webovou aplikaci](scenario-secure-app-authentication-app-service.md)
