---
title: Vytvoření aplikace pro Android
description: V tomto kurzu se naučíte používat Mobilní aplikace Azure back-endy pro vývoj aplikací pro Android.
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 9be9402bff1a1263de3c6f21b78899464c50c823
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459562"
---
# <a name="create-an-android-app"></a>Vytvoření aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu do mobilní aplikace Android pomocí back-endu mobilní aplikace Azure.  Vytvoříte jak nový back-end mobilní aplikace, tak jednoduchou aplikaci pro Android, která bude představovat *seznam úkolů* a ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Android.

## <a name="prerequisites"></a>Předpoklady
Pro absolvování tohoto kurzu potřebujete:

* [Android Developer Tools](https://developer.android.com/sdk/index.html), což zahrnuje integrované vývojové prostředí Android Studio a nejnovější platformu Android
* Mobilní Android SDK Azure.
* [Aktivní účet Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace klientského a serverového projektu
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Spuštění aplikace pro Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
