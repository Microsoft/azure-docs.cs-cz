---
title: Začínáme s aplikacemi pro Xamarin. iOS
description: V tomto kurzu začnete používat Mobile Apps pro vývoj na platformě Xamarin.iOS.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 6c35189e7c841fa2724f1cfe84afc689d5510676
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459000"
---
# <a name="create-a-xamarinios-app"></a>Vytvoření aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu do mobilní aplikace Xamarin.iOS pomocí back-endu mobilní aplikace Azure.  Vytvoříte nový back-end mobilní aplikace i jednoduchou aplikaci Xamarin.iOS, která bude představovat *seznam úkolů* a bude ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Xamarin.iOS.

## <a name="prerequisites"></a>Předpoklady
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio for Mac. Viz [nastavení a instalace pro Visual Studio pro Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Počítač Mac s Xcode 9,0 nebo novějším.
  
## <a name="create-an-azure-mobile-app-backend"></a>Vytvoření back-endu mobilní aplikace Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace klientského a serverového projektu
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Spuštění aplikace Xamarin. iOS
1. Otevřete projekt Xamarin. iOS.

2. Přejděte na [Azure Portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. V okně `Overview` vyhledejte adresu URL, která je pro vaši mobilní aplikaci veřejným koncovým bodem. Příklad – název vaší aplikace "test123" bude https://test123.azurewebsites.net.

3. Otevřete soubor `QSTodoService.cs` v této složce – Xamarin. iOS/ZUMOAPPNAME. Název aplikace je `ZUMOAPPNAME`.

4. V `QSTodoService` třídy nahraďte `ZUMOAPPURL` proměnnou veřejným koncovým bodem výše.

    `const string applicationURL = @"ZUMOAPPURL";`

    stane
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Stisknutím klávesy F5 nasaďte a spusťte aplikaci v emulátoru iPhonu.

6. V aplikaci zadejte smysluplný text, například *dokončit kurz* , a klikněte na tlačítko +.

    ![][10]

    Data z požadavku se vloží do tabulky TodoItem. Položky uložené v tabulce budou vráceny back-endu mobilní aplikace a v seznamu se objeví data.

   > [!NOTE]
   > Na kód, který přistupuje k back-endu mobilní aplikace pro dotazování a vkládání dat, se můžete podívat v souboru C# ToDoActivity.cs.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png
