---
title: Začínáme s aplikacemi Xamarin.Android
description: Podle tohoto kurzu můžete začít používat Azure Mobile Apps pro vývoj Xamarin Android.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: b42205436c88f9075423bfcaf9e5a9fd931ee4f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461364"
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
Podle těchto pokynů vytvořte back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Nyní máte zřízen back-end mobilní aplikace Azure, který je možné použít v mobilních klientských aplikacích. Dále si stáhněte serverový projekt pro jednoduchý back-end seznamu úkolů a publikujete ho v Azure.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace projektu klienta a serveru
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Spuštění aplikace Xamarin.Android
1. Otevřete projekt Xamarin.Android.

2. Přejděte na [portál Azure](https://portal.azure.com/) a přejděte na mobilní aplikaci, kterou jste vytvořili. Na `Overview` okně vyhledejte adresu URL, která je veřejným koncovým bodem pro vaši mobilní aplikaci. Příklad – název webu pro název aplikace "test123" bude https://test123.azurewebsites.net.

3. Otevřete `ToDoActivity.cs` soubor v této složce - xamarin.android/ZUMOAPPNAME/ToDoActivity.cs. Název aplikace `ZUMOAPPNAME`je .

4. Ve `ToDoActivity` třídě `ZUMOAPPURL` nahraďte proměnnou veřejným koncovým bodem výše.

    `const string applicationURL = @"ZUMOAPPURL";`

    se stává
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Stisknutím klávesy F5 nasadíte a spusťte aplikaci.

6. V aplikaci zadejte smysluplný text, například *Dokončit kurz,* a klikněte na tlačítko **Přidat.**

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
