---
title: Vytvoření aplikace pro Android v Azure App Service Mobile Apps | Dokumentace Microsoftu
description: V tomto kurzu začnete používat back-endy mobilní aplikace Azure pro vývoj na platformě Android.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: crdun
ms.openlocfilehash: 72fd043321aefe74e95b34ec24c7be2ffd409439
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240338"
---
# <a name="create-an-android-app"></a>Vytvoření aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu do mobilní aplikace Android pomocí back-endu mobilní aplikace Azure.  Vytvoříte jak nový back-end mobilní aplikace, tak jednoduchou aplikaci pro Android, která bude představovat *seznam úkolů* a ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Android.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* [Android Developer Tools](https://developer.android.com/sdk/index.html), což zahrnuje integrované vývojové prostředí Android Studio a nejnovější platformu Android
* Azure Mobile Android SDK.
* [Aktivní účet Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Spuštění aplikace pro Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
