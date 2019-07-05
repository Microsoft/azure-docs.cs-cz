---
title: Začínáme s Azure Mobile Apps u aplikací na platformě Xamarin.Android
description: V tomto kurzu začnete používat Azure Mobile Apps pro vývoj na platformě Xamarin.Android.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: eed900ee54f62056eceeb35a43a4ba6526b049ca
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447089"
---
# <a name="create-a-xamarinandroid-app"></a>Vytvoření aplikace Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center investuje do nové a integrované služby, které jsou centrální při vývoji mobilních aplikací. Vývojáři mohou použít **sestavení**, **testovací** a **rozmístit** služby vytvořit kanál pro průběžnou integraci a doručování. Po nasazení aplikace se můžou vývojáři monitorovat stav a využití své aplikace pomocí **Analytics** a **diagnostiky** služeb a Zaujměte uživatele, kteří používají **Push** Služba. Vývojáři mohou využít i **Auth** k ověření uživatelů a **Data** službu zachovat, synchronizaci dat aplikací v cloudu. Podívejte se na [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started) ještě dnes.
>

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
