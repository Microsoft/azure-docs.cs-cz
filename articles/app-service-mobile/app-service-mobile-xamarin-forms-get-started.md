---
title: Začínáme s Xamarin. Forms
description: V tomto kurzu začnete používat funkci Mobile Apps pro vývoj s Xamarin.Forms.
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 9ae97ccbcc358a150e914a6e950579a972fef0bc
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461330"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Vytvoření aplikace na platformě Xamarin.Forms s využitím Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou službu back-end do mobilní aplikace na platformě Xamarin.Forms pomocí funkce Mobile Apps služby Azure App Service jako back-endu. Vytvoříte jak nový back-end Mobile Apps, tak jednoduchou aplikaci Xamarin.Forms, která bude představovat seznam úkolů a ukládat data do Azure.

Ve všech dalších kurzech k Mobile Apps týkajících se Xamarin.Forms se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Předpoklady

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Další informace najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio Tools pro Xamarin, Visual Studio 2017 nebo novější nebo Visual Studio pro Mac. Pokyny najdete na [stránce instalace Xamarin][Install Xamarin] .

* (Volitelné) K sestavení aplikace pro iOS se vyžaduje Mac s prostředím Xcode 9.0 nebo novějším. Visual Studio pro Mac lze použít k vývoji aplikací pro iOS, nebo můžete použít Visual Studio 2017 nebo novější (Pokud je počítač Mac k dispozici v síti).

## <a name="create-a-new-mobile-apps-back-end"></a>Vytvoření nového back-endu Mobile Apps
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Vytvoření připojení k databázi a konfigurace klientského a serverového projektu
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinforms-solution"></a>Spuštění řešení Xamarin. Forms

K otevření řešení se vyžaduje Visual Studio Tools pro Xamarin. Projděte si [pokyny k instalaci Xamarin][Install Xamarin]. Pokud jsou tyto nástroje už nainstalované, použijte ke stažení a otevření tohoto řešení následující postup:

### <a name="visual-studio-windows-and-mac"></a>Visual Studio (Windows a Mac)

1. Přejděte na [Azure Portal](https://portal.azure.com/) a přejděte do mobilní aplikace, kterou jste vytvořili. V okně `Overview` vyhledejte adresu URL, která je pro vaši mobilní aplikaci veřejným koncovým bodem. Příklad – název vaší aplikace "test123" bude https://test123.azurewebsites.net.

2. Otevřete soubor `Constants.cs` v této složce – Xamarin. Forms/ZUMOAPPNAME. Název aplikace je `ZUMOAPPNAME`.

3. V `Constants.cs` třídy nahraďte `ZUMOAPPURL` proměnnou veřejným koncovým bodem výše.

    `public static string ApplicationURL = @"ZUMOAPPURL";`

    stane

    `public static string ApplicationURL = @"https://test123.azurewebsites.net";`
    
4. Při spouštění projektů pro Android nebo Windows (a také pro iOS, pokud je v síti k dispozici počítač Mac) použijte následující pokyny.

## <a name="optional-run-the-android-project"></a>(Nepovinné) Spuštění projektu pro Android

V této části spustíte projekt Xamarin.Android. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

### <a name="visual-studio"></a>Visual Studio

1. Klikněte pravým tlačítkem na projekt pro Android (Droid) a pak vyberte **Nastavit jako spouštěný projekt**.

2. V nabídce **Sestavení** (Build) vyberte **Správce konfigurace**.

3. V dialogovém okně **Správce konfigurace** zaškrtněte políčka **Sestavení** a **Nasazení** vedle projektu pro Android a zkontrolujte, že projekt sdíleného kódu má zaškrtnuté políčko **Sestavení**.

4. Stisknutím klávesy **F5** nebo kliknutím na tlačítko **Start** se projekt sestaví a aplikace se spustí v emulátoru Androidu.

### <a name="visual-studio-for-mac"></a>Visual Studio pro Mac

1. Klikněte pravým tlačítkem na projekt pro Android a pak vyberte **Nastavit jako spouštěný projekt**.

2. Pokud chcete sestavit projekt a spustit aplikaci v emulátoru Androidu, v nabídce **Spustit** vyberte **Spustit ladění**.

Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus ( **+** ).

![Aplikace seznamu úkolů pro Android][11]

Touto akcí se odešle požadavek POST na nový back-end Mobile Apps, jehož hostitelem je Azure. Data z požadavku se vloží do tabulky TodoItem. Back-end Mobile Apps vrátí položky uložené v tabulce a data se zobrazí v seznamu.

> [!NOTE]
> Kód, který získává přístup k back-endu Mobile Apps, najdete v souboru jazyka C# **TodoItemManager.cs** v projektu sdíleného kódu pro vaše řešení.
>

## <a name="optional-run-the-ios-project"></a>(Nepovinné) Spuštění projektu pro iOS

V této části spustíte projekt Xamarin.iOS pro zařízení s iOS. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

### <a name="visual-studio"></a>Visual Studio

1. Klikněte pravým tlačítkem na projekt pro iOS a pak vyberte **Nastavit jako spouštěný projekt**.

2. V nabídce **Sestavení** (Build) vyberte **Správce konfigurace**.

3. V dialogovém okně **Správce konfigurace** zaškrtněte políčka **Sestavení** a **Nasazení** vedle projektu pro iOS a zkontrolujte, že projekt sdíleného kódu má zaškrtnuté políčko **Sestavení**.

4. Pokud chcete projekt sestavit a spustit aplikaci v emulátoru iPhonu, stiskněte klávesu **F5**.

### <a name="visual-studio-for-mac"></a>Visual Studio pro Mac

1. Klikněte pravým tlačítkem na projekt pro iOS a pak vyberte **Nastavit jako spouštěný projekt**.

2. V nabídce **Spustit** vyberte **Spustit ladění**, aby se projekt sestavil a aplikace se spustila v emulátoru iPhonu.

Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus ( **+** ).

![Aplikace seznamu úkolů pro iOS][10]

Touto akcí se odešle požadavek POST na nový back-end Mobile Apps, jehož hostitelem je Azure. Data z požadavku se vloží do tabulky TodoItem. Back-end Mobile Apps vrátí položky uložené v tabulce a data se zobrazí v seznamu.

> [!NOTE]
> Kód, který získává přístup k back-endu Mobile Apps, najdete v souboru jazyka C# **TodoItemManager.cs** v projektu sdíleného kódu pro vaše řešení.
>

## <a name="optional-run-the-windows-project"></a>(Nepovinné) Spuštění projektu pro Windows

V této části spustíte projekt Xamarin.Forms Univerzální platformy Windows (UPW) pro zařízení s Windows. Můžete ji přeskočit, pokud s takovými zařízeními nepracujete.

### <a name="visual-studio"></a>Visual Studio

1. Klikněte pravým tlačítkem na projekt pro UPW a pak vyberte **Nastavit jako spouštěný projekt**.

2. V nabídce **Sestavení** (Build) vyberte **Správce konfigurace**.

3. V dialogovém okně **Správce konfigurace** zaškrtněte políčka **Sestavení** a **Nasazení** vedle zvoleného projektu pro Windows a zkontrolujte, že projekt sdíleného kódu má zaškrtnuté políčko **Sestavení**.

4. Stisknutím klávesy **F5** nebo kliknutím na tlačítko **Start** (na kterém by měl být text **Místní počítač**) se projekt sestaví a aplikace se spustí v emulátoru Windows.

> [!NOTE]
> Projekt pro Windows nejde spustit v macOS.

Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus ( **+** ).

Touto akcí se odešle požadavek POST na nový back-end Mobile Apps, jehož hostitelem je Azure. Data z požadavku se vloží do tabulky TodoItem. Back-end Mobile Apps vrátí položky uložené v tabulce a data se zobrazí v seznamu.

![Aplikace seznamu úkolů pro UPW][12]

> [!NOTE]
> Kód, který získává přístup k back-endu Mobile Apps, najdete v souboru jazyka C# **TodoItemManager.cs** v projektu knihovny přenosných tříd pro vaše řešení.
>

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte se sestavením řešení problémy, spusťte správce balíčků NuGet a proveďte aktualizaci na nejnovější verz i`Xamarin.Forms`. V projektu pro Android aktualizujte balíčky pro podporu `Xamarin.Android`. Projekty Rychlý start nezahrnují vždycky nejnovější verze.

Upozorňujeme, že všechny balíčky podporu odkazované ve vašem projektu Android musí mít stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/) má závislost `Xamarin.Android.Support.CustomTabs` pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
