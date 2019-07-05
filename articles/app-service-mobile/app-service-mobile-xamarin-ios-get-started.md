---
title: Začínáme s Azure App Service Mobile Apps pro aplikace na platformě Xamarin.iOS | Dokumentace Microsoftu
description: V tomto kurzu začnete používat Mobile Apps pro vývoj na platformě Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 57867eeca9f29cfc3983cbdca94c830aa7a20500
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446245"
---
# <a name="create-a-xamarinios-app"></a>Vytvoření aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investuje do nové a integrované služby, které jsou centrální při vývoji mobilních aplikací. Vývojáři mohou použít **sestavení**, **testovací** a **rozmístit** služby vytvořit kanál pro průběžnou integraci a doručování. Po nasazení aplikace se můžou vývojáři monitorovat stav a využití své aplikace pomocí **Analytics** a **diagnostiky** služeb a Zaujměte uživatele, kteří používají **Push** Služba. Vývojáři mohou využít i **Auth** k ověření uživatelů a **Data** službu zachovat, synchronizaci dat aplikací v cloudu. Podívejte se na [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started) ještě dnes.
>

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu do mobilní aplikace Xamarin.iOS pomocí back-endu mobilní aplikace Azure.  Vytvoříte nový back-end mobilní aplikace i jednoduchou aplikaci Xamarin.iOS, která bude představovat *seznam úkolů* a bude ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Xamarin.iOS.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Zobrazit [nastavení a instalaci sady Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Počítač Mac s Xcode 9.0 nebo novější.
  
## <a name="create-an-azure-mobile-app-backend"></a>Vytvoření back-endu mobilní aplikace Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Spuštění aplikace Xamarin.iOS
1. Otevřete projekt Xamarin.iOS.

2. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

3. Otevřete soubor `QSTodoService.cs` v této složce - xamarin.iOS/ZUMOAPPNAME. Název aplikace je `ZUMOAPPNAME`.

4. V `QSTodoService` třídy, nahraďte `ZUMOAPPURL` proměnné s veřejným koncovým bodem výše.

    `const string applicationURL = @"ZUMOAPPURL";`

    změní
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Stisknutím klávesy F5 nasadit a spustit aplikaci v emulátoru Iphonu.

6. V aplikaci zadejte smysluplný text, *absolvování tohoto kurzu* a klikněte tlačítko +.

    ![][10]

    Data z požadavku se vloží do tabulky TodoItem. Položky uložené v tabulce budou vráceny back-endu mobilní aplikace a v seznamu se objeví data.

   > [!NOTE]
   > Na kód, který přistupuje k back-endu mobilní aplikace pro dotazování a vkládání dat, se můžete podívat v souboru C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png