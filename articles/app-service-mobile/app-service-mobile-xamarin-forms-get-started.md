---
title: Začínáme s funkcí Mobile Apps na platformě Xamarin.Forms
description: V tomto kurzu začnete používat funkci Mobile Apps pro vývoj s Xamarin.Forms.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 68ae5628d8b6aeeec6c1549e3d29d48bc8906a2d
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52994204"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Vytvoření aplikace na platformě Xamarin.Forms s využitím Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

V tomto kurzu se dozvíte, jak přidat cloudovou službu back-end do mobilní aplikace na platformě Xamarin.Forms pomocí funkce Mobile Apps služby Azure App Service jako back-endu. Vytvoříte jak nový back-end Mobile Apps, tak jednoduchou aplikaci Xamarin.Forms, která bude představovat seznam úkolů a ukládat data do Azure.

Ve všech dalších kurzech k Mobile Apps týkajících se Xamarin.Forms se předpokládá dokončení tohoto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Další informace najdete na stránce [bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio Tools for Xamarin, v sadě Visual Studio 2017 nebo Visual Studio pro Mac. Pokyny najdete na [stránce věnované instalaci Xamarinu][Install Xamarin].

* (Volitelné) K sestavení aplikace pro iOS se vyžaduje Mac s prostředím Xcode 9.0 nebo novějším. K vývoji aplikací pro iOS se dá použít Visual Studio pro Mac nebo Visual Studio 2017 (za předpokladu, že Mac je k dispozici v síti).

## <a name="create-a-new-mobile-apps-back-end"></a>Vytvoření nového back-endu Mobile Apps

Pokud chcete vytvořit nový back-end Mobile Apps, postupujte následovně:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Právě jste nastavili back-end Mobile Apps, který mohou používat vaše mobilní aplikace. Dále si stáhnete serverový projekt pro jednoduchý back-end seznamu úkolů a pak ho publikujete v Azure.

## <a name="configure-the-server-project"></a>Konfigurace serverového projektu

Pokud chcete nakonfigurovat serverový projekt tak, aby používal buď back-end v Node.js, nebo v .NET, postupujte následovně:

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Stáhnutí a spuštění řešení Xamarin.Forms

K otevření řešení se vyžadují nástroje Visual Studio Tools for Xamarin, viz [pokyny k instalaci Xamarinu][Install Xamarin]. Pokud jsou tyto nástroje už nainstalované, použijte ke stažení a otevření tohoto řešení následující postup:

### <a name="visual-studio"></a>Visual Studio

1. Přejděte na [Azure Portal].

2. V okně nastavení mobilní aplikace klikněte na **Rychlý start** (v části Nasazení) > **Xamarin.Forms**. V kroku 3 klikněte na možnost **Vytvořit novou aplikaci**, pokud ještě nebyla vybrána.  Pak klikněte na tlačítko **Stáhnout**.

   Touto akcí se stáhne projekt, který obsahuje klientskou aplikaci připojenou k mobilní aplikaci. Uložte komprimovaný soubor projektu do místního počítače a poznamenejte si, kam jste jej uložili.

3. Extrahujte projekt, který jste stáhli, a otevřete ho v sadě Visual Studio 2017.

   ![Extrahovaný projekt v sadě Visual Studio][8]

4. Při spouštění projektů pro Android nebo Windows (a také pro iOS, pokud je v síti k dispozici počítač Mac) použijte následující pokyny.

### <a name="visual-studio-for-mac"></a>Visual Studio pro Mac

1. Přejděte na [Azure Portal].

2. V okně nastavení mobilní aplikace klikněte na **Rychlý start** (v části Nasazení) > **Xamarin.Forms**. V kroku 3 klikněte na možnost **Vytvořit novou aplikaci**, pokud ještě nebyla vybrána.  Pak klikněte na tlačítko **Stáhnout**.

   Touto akcí se stáhne projekt, který obsahuje klientskou aplikaci připojenou k mobilní aplikaci. Uložte komprimovaný soubor projektu do místního počítače a poznamenejte si, kam jste jej uložili.

3. Extrahujte projekt, který jste stáhli, a otevřete ho v sadě Visual Studio pro Mac.

   ![Extrahovaný projekt v sadě Visual Studio pro Mac][9]

4. Při spouštění projektů pro Android nebo iOS postupujte podle následujících pokynů.



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



Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus (**+**).

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



Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus (**+**).

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



Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a pak vyberte symbol plus (**+**).

Touto akcí se odešle požadavek POST na nový back-end Mobile Apps, jehož hostitelem je Azure. Data z požadavku se vloží do tabulky TodoItem. Back-end Mobile Apps vrátí položky uložené v tabulce a data se zobrazí v seznamu.

![Aplikace seznamu úkolů pro UPW][12]

> [!NOTE]
> Kód, který získává přístup k back-endu Mobile Apps, najdete v souboru jazyka C# **TodoItemManager.cs** v projektu knihovny přenosných tříd pro vaše řešení.
>

## <a name="troubleshooting"></a>Řešení potíží

Pokud máte se sestavením řešení problémy, spusťte správce balíčků NuGet a proveďte aktualizaci na nejnovější verz i`Xamarin.Forms`. V projektu pro Android aktualizujte balíčky pro podporu `Xamarin.Android`. Projekty Rychlý start nezahrnují vždycky nejnovější verze.

Upozorňujeme, že všechny balíčky podporu odkazované ve vašem projektu Android musí mít stejnou verzi. [Balíček NuGet pro mobilní aplikace Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)má`Xamarin.Android.Support.CustomTabs`závislost pro platformu Android, takže pokud váš projekt používá novější balíčky podpory, je nutné nainstalovat přímo tento balíček s požadovanou verzi, aby nedocházelo ke konfliktům.

## <a name="next-steps"></a>Další postup

* [Přidání ověřování do vaší aplikace](app-service-mobile-xamarin-forms-get-started-users.md) Zjistěte, jak ověřovat uživatele vaší aplikace pomocí zprostředkovatele identity.

* [Přidání nabízených oznámení do vaší aplikace](app-service-mobile-xamarin-forms-get-started-push.md) Zjistěte, jak do aplikace přidat podporu nabízených oznámení a jak nakonfigurovat back-end Mobile Apps tak, aby k zasílání nabízených oznámení používal službu Azure Notification Hubs.

* [Povolení offline synchronizace pro vaši aplikaci](app-service-mobile-xamarin-forms-get-started-offline-data.md) Zjistěte, jak pomocí back-endu Mobile Apps přidat do aplikace podporu offline režimu. Offline synchronizace umožňuje zobrazovat, přidávat nebo upravovat data mobilní aplikace i když nemá připojení k síti.

* [Použití spravovaného klienta pro Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md) Naučte se pracovat se spravovanou klientskou sadou SDK v aplikaci Xamarin.

* [Použití dalších služeb Azure s Xamarin.Forms](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) Přidání dalších funkcí Azure, jako je hledání, úložiště a kognitivní služby, do aplikací na platformě Xamarin.Forms.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
