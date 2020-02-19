---
title: Vytvoření aplikace pro UWP
description: V tomto kurzu začnete pracovat s back-endy mobilní aplikace Azure při vývoji aplikací pro Univerzální platformu Windows (UPW) v jazycích C#, Visual Basic a JavaScript.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9188db19adab9bd46d65fc97f1c62b39141cee90
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461381"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Vytvoření aplikace pro Windows pomocí back-endu Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak do aplikace pro Univerzální platformu Windows (UPW) přidat cloudovou back-end službu. Další informace najdete v tématu [Co jsou Mobile Apps](app-service-mobile-value-prop.md). Zde jsou snímky obrazovek z hotové aplikace:

![Hotová desktopová aplikace](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Ve všech dalších kurzech Mobile App pro aplikace UPW se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Znalost vývoje aplikací pro UWP Informace o tom, jak [nastavit](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) vytváření aplikací pro UWP, najdete v [dokumentaci k UWP](https://docs.microsoft.com/windows/uwp/) .

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure

Podle těchto pokynů vytvořte nový back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace klientského a serverového projektu
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Spustit klientský projekt

1. Otevřete projekt UWP.

2. Přejděte na [Azure Portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. V okně `Overview` vyhledejte adresu URL, která je pro vaši mobilní aplikaci veřejným koncovým bodem. Příklad – název vaší aplikace "test123" bude https://test123.azurewebsites.net.

3. Otevřete soubor `App.xaml.cs` v této složce-Windows-UWP-cs/ZUMOAPPNAME/. Název aplikace je `ZUMOAPPNAME`.

4. V `App` třídy nahraďte `ZUMOAPPURL` parametr veřejným koncovým bodem výše.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    stane
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Stisknutím klávesy F5 nasaďte a spusťte aplikaci.

6. V aplikaci zadejte do textového pole pro *vložení úkolu* smysluplný text, například **Dokončit kurz**, a klikněte na **Uložit**.

    ![Dokončení desktopového projektu s rychlým startem pro Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Tím se odešle požadavek POST na nový back-end mobilní aplikace, jehož hostitelem je Azure.
