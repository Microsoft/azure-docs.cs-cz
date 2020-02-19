---
title: Vytvoření aplikace Cordova
description: V tomto kurzu začnete používat back-endy mobilních aplikací Azure pro vývoj na platformě Apache Cordova.
keywords: cordova, javascript, mobilní, klient
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 99fb4a4b07ecbd4a85abbc62ec52a0f5960654c5
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461568"
---
# <a name="create-an-apache-cordova-app"></a>Vytvoření aplikace na platformě Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu k mobilní aplikaci Apache Cordova pomocí back-endu mobilní aplikace Azure.  Vytvoříte jak nový back-end mobilní aplikace, tak jednoduchou aplikaci Apache Cordova, která bude představovat *seznam úkolů* a ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Apache Cordova.

## <a name="prerequisites"></a>Předpoklady
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Počítač se sadou [Visual Studio Community 2017] nebo novější
* [Visual Studio Tools for Apache Cordova]
* [Aktivní účet Azure](https://azure.microsoft.com/pricing/free-trial/)

Visual Studio je možné obejít a používat přímo příkazový řádek platformy Apache Cordova.  Použití příkazového řádku je užitečné v případě, že kurz budete absolvovat na počítači Mac.  Tento kurz se nezabývá kompilací klientských aplikací Apache Cordova pomocí příkazového řádku.

## <a name="create-an-azure-mobile-app-backend"></a>Vytvoření back-endu mobilní aplikace Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace klientského a serverového projektu
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Stažení a spuštění aplikace Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools for Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
