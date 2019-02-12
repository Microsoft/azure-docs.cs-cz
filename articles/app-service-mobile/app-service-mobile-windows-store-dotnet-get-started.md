---
title: Vytvoření Universal Windows Platform (UPW) využívající Azure Mobile Apps | Dokumentace Microsoftu
description: V tomto kurzu začnete pracovat s back-endy mobilní aplikace Azure při vývoji aplikací pro Univerzální platformu Windows (UPW) v jazycích C#, Visual Basic a JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/17/2018
ms.author: crdun
ms.openlocfilehash: 00b635853d0dfad73a258ddc8ef9b168559fc5df
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098421"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Vytvoření aplikace pro Windows s back-endu Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak do aplikace pro Univerzální platformu Windows (UPW) přidat cloudovou back-end službu. Další informace najdete v tématu [Co jsou Mobile Apps](app-service-mobile-value-prop.md). Zde jsou snímky obrazovek z hotové aplikace:

![Hotová desktopová aplikace](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Ve všech dalších kurzech Mobile App pro aplikace UPW se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* [Visual Studio Community 2017].
* Znalost vývoj aplikací pro UPW. Přejděte [dokumentace k UPW](https://docs.microsoft.com/windows/uwp/) další postupy [nastavení](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) a vytvářet aplikace pro UPW.

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure

Podle těchto pokynů vytvořte nový back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Nyní máte zřízen back-end mobilní aplikace Azure, který je možné použít v mobilních klientských aplikacích. Dále si stáhnete serverový projekt pro jednoduchý back-end seznamu úkolů a publikujete ho v Azure.

## <a name="configure-the-server-project"></a>Konfigurace serverového projektu

[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-client-project"></a>Stažení a spuštění klientského projektu

Až nakonfigurujete svůj back-end mobilní aplikace, je možné buď vytvořit novou klientskou aplikaci, nebo upravit existující aplikaci tak, aby se připojovala k Azure. V této části si stáhnete projekt ukázkové aplikace UPW přizpůsobený tak, aby se připojoval k vašemu back-endu mobilní aplikace.

1. Po návratu do okna **Rychlý start** back-endu mobilní aplikace klikněte na **Vytvořit novou aplikaci** > **Stáhnout** a extrahujte komprimované soubory projektu do místního počítače.

    ![Stáhnutí projektu typu rychlý start pro Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-app-windows-quickstart.png)

2. Otevřete projekt UPW a stisknutím klávesy F5 nasaďte a spusťte aplikaci.
3. V aplikaci zadejte do textového pole pro **vložení úkolu** smysluplný text, například *Dokončit kurz*, a klikněte na **Uložit**.

    ![Dokončení desktopového projektu s rychlým startem pro Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Tím se odešle požadavek POST na nový back-end mobilní aplikace, jehož hostitelem je Azure.

> [!TIP]
> Pokud používáte back-end .NET, můžete projekt aplikace UPW přidat do stejného řešení, ve kterém se nachází serverový projekt. Ve stejném řešení sady Visual Studio bude snazší ladit a testovat jak aplikaci, tak back-end. Pokud chcete přidat projekt aplikace UPW do řešení back-endu, musíte použít sadu Visual Studio 2017.

## <a name="next-steps"></a>Další postup

* [Přidání ověřování do aplikace](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.
* [Přidání nabízených oznámení do aplikace](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Naučte se přidávat do aplikace podporu nabízených oznámení a konfigurovat back-end mobilní aplikace tak, aby k zasílání nabízených oznámení používal Azure Notification Hubs.
* [Povolení offline synchronizace u aplikace](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Naučte se, jak pomocí back-endu mobilní aplikace přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje koncovým uživatelům pracovat s mobilní aplikací &mdash; zobrazovat, přidávat a upravovat data &mdash; i v případě, že nemají připojení k síti.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->
[Mobile App SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://go.microsoft.com/fwLink/p/?LinkID=534203
