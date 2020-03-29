---
title: Vytvoření aplikace pro iOS
description: Postupujte podle tohoto kurzu a můžete začít s používáním backendů mobilních aplikací Azure pro vývoj iOS v objective-C nebo Swiftu.
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1b1114a22d33689f485aa228a8a1cf65eba719da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461483"
---
# <a name="create-an-ios-app"></a>Vytvoření aplikace pro iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]
## <a name="overview"></a>Přehled

Tento kurz ukazuje, jak do aplikace pro iOS přidat cloudovou back-end službu [Azure App Service Mobile Apps](app-service-mobile-value-prop.md). Prvním krokem je vytvoření nového mobilního back-endu v Azure. Potom stáhnete ukázkovou aplikaci *seznamu úkolů*, která ukládá data v Azure.

K dokončení tohoto kurzu potřebujete Mac a [účet Azure ](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Spuštění aplikace pro iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
