---
title: Vytvoření aplikace pro UPW
description: V tomto kurzu začnete pracovat s back-endy mobilní aplikace Azure při vývoji aplikací pro Univerzální platformu Windows (UPW) v jazycích C#, Visual Basic a JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461381"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Vytvoření aplikace pro Windows s back-endem Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak do aplikace pro Univerzální platformu Windows (UPW) přidat cloudovou back-end službu. Další informace najdete v tématu [Co jsou Mobile Apps](app-service-mobile-value-prop.md). Zde jsou snímky obrazovek z hotové aplikace:

![Hotová desktopová aplikace](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Ve všech dalších kurzech Mobile App pro aplikace UPW se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Znalost vývoje aplikací UPW. Navštivte [dokumentaci UPW,](https://docs.microsoft.com/windows/uwp/) kde se [dozvíte,](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) jak nastavit vytváření aplikací UPW.

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure

Podle těchto pokynů vytvořte nový back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Spuštění klientského projektu

1. Otevřete projekt UPW.

2. Přejděte na [portál Azure](https://portal.azure.com/) a přejděte na mobilní aplikaci, kterou jste vytvořili. Na `Overview` okně vyhledejte adresu URL, která je veřejným koncovým bodem pro vaši mobilní aplikaci. Příklad – název webu pro název aplikace "test123" bude https://test123.azurewebsites.net.

3. Otevřete `App.xaml.cs` soubor v této složce - windows-uwp-cs/ZUMOAPPNAME/. Název aplikace `ZUMOAPPNAME`je .

4. Ve `App` třídě `ZUMOAPPURL` nahraďte parametr veřejným koncovým bodem výše.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    se stává
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Stisknutím klávesy F5 nasadíte a spusťte aplikaci.

6. V aplikaci zadejte do textového pole pro **vložení úkolu** smysluplný text, například *Dokončit kurz*, a klikněte na **Uložit**.

    ![Dokončení desktopového projektu s rychlým startem pro Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Tím se odešle požadavek POST na nový back-end mobilní aplikace, jehož hostitelem je Azure.
