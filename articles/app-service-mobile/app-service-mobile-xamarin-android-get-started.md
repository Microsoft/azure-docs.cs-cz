---
title: Začínáme s Azure Mobile Apps u aplikací na platformě Xamarin.Android
description: V tomto kurzu začnete používat Azure Mobile Apps pro vývoj na platformě Xamarin.Android.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: f3e8ca4f9736dffe4928fc8920b0890dff87367b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236027"
---
# <a name="create-a-xamarinandroid-app"></a>Vytvoření aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak do aplikace Xamarin.Android přidat cloudovou back-end službu. Další informace najdete v tématu [Co jsou Mobile Apps](app-service-mobile-value-prop.md).

Zde je snímek obrazovky dokončené aplikace:

![][0]

Ve všech dalších kurzech k používání funkce Mobile Apps pro Xamarin.Android se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio s Xamarinem. Pokyny najdete v tématu o [nastavení a instalaci pro Visual Studio a Xamarin](/visualstudio/cross-platform/setup-and-install).

## <a name="create-an-azure-mobile-app-backend"></a>Vytvoření back-endu mobilní aplikace Azure
Podle těchto pokynů vytvořte back-end mobilní aplikace.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Nyní máte zřízen back-end mobilní aplikace Azure, který je možné použít v mobilních klientských aplikacích. Dále si stáhněte serverový projekt pro jednoduchý back-end seznamu úkolů a publikujete ho v Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Spuštění aplikace Xamarin.Android
1. Otevřete projekt Xamarin.Android.

2. Přejděte [webu Azure portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. Na `Overview` okno Vyhledat adresu URL, které je veřejný koncový bod pro mobilní aplikace. Například - sitename pro Moje aplikace název "test123" bude https://test123.azurewebsites.net.

3. Otevřete soubor `ToDoActivity.cs` v této složce - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Název aplikace je `ZUMOAPPNAME`.

4. V `ToDoActivity` třídy, nahraďte `ZUMOAPPURL` proměnné s veřejným koncovým bodem výše.

    `const string applicationURL = @"ZUMOAPPURL";`

    změní
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Stisknutím klávesy F5 pro nasazení a spuštění aplikace.

6. V aplikaci zadejte smysluplný text, například *Dokončit kurz*, a klikněte na tlačítko **Přidat**.

    ![][10]

    Data z požadavku se vloží do tabulky TodoItem. Položky uložené v tabulce budou vráceny back-endu mobilní aplikace a v seznamu se objeví data.

   > [!NOTE]
   > Na kód, který přistupuje k back-endu mobilní aplikace pro dotazování a vkládání dat, se můžete podívat v souboru C# ToDoActivity.cs.
   
## <a name="troubleshooting"></a>Řešení potíží
Pokud máte se sestavením řešení problémy, spusťte správce balíčků NuGet a proveďte aktualizaci`Xamarin.Android` balíčků pro podporu. Projekty Rychlý start nezahrnují vždycky nejnovější verze.

Upozorňujeme, že všechny balíčky podpory odkazované ve vašem projektu musí mít stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)má`Xamarin.Android.Support.CustomTabs`závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
