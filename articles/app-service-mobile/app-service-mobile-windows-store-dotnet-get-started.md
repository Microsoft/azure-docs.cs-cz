---
title: Vytvoření Universal Windows Platform (UPW) využívající Azure Mobile Apps | Dokumentace Microsoftu
description: V tomto kurzu začnete pracovat s back-endy mobilní aplikace Azure při vývoji aplikací pro Univerzální platformu Windows (UPW) v jazycích C#, Visual Basic a JavaScript.
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 2b718a000bb5805cc615a59aebb2d5dcca6906a4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440213"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Vytvoření aplikace pro Windows s back-endu Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investuje do nové a integrované služby, které jsou centrální při vývoji mobilních aplikací. Vývojáři mohou použít **sestavení**, **testovací** a **rozmístit** služby vytvořit kanál pro průběžnou integraci a doručování. Po nasazení aplikace se můžou vývojáři monitorovat stav a využití své aplikace pomocí **Analytics** a **diagnostiky** služeb a Zaujměte uživatele, kteří používají **Push** Služba. Vývojáři mohou využít i **Auth** k ověření uživatelů a **Data** službu zachovat, synchronizaci dat aplikací v cloudu. Podívejte se na [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started) ještě dnes.
>

## <a name="overview"></a>Přehled

V tomto kurzu se dozvíte, jak do aplikace pro Univerzální platformu Windows (UPW) přidat cloudovou back-end službu. Další informace najdete v tématu [Co jsou Mobile Apps](app-service-mobile-value-prop.md). Zde jsou snímky obrazovek z hotové aplikace:

![Hotová desktopová aplikace](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

Ve všech dalších kurzech Mobile App pro aplikace UPW se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Znalost vývoj aplikací pro UPW. Přejděte [dokumentace k UPW](https://docs.microsoft.com/windows/uwp/) další postupy [nastavení](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) a vytvářet aplikace pro UPW.

## <a name="create-a-new-azure-mobile-app-backend"></a>Vytvoření nového back-endu mobilní aplikace Azure

Podle těchto pokynů vytvořte nový back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Spuštění klientského projektu

1. Otevřete projekt UPW.

2. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

3. Otevřete soubor `App.xaml.cs` v této složce - windows UPW cs/ZUMOAPPNAME /. Název aplikace je `ZUMOAPPNAME`.

4. V `App` třídy, nahraďte `ZUMOAPPURL` parametr s veřejným koncovým bodem výše.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    změní
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Stisknutím klávesy F5 pro nasazení a spuštění aplikace.

6. V aplikaci zadejte do textového pole pro **vložení úkolu** smysluplný text, například *Dokončit kurz*, a klikněte na **Uložit**.

    ![Dokončení desktopového projektu s rychlým startem pro Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Tím se odešle požadavek POST na nový back-end mobilní aplikace, jehož hostitelem je Azure.
