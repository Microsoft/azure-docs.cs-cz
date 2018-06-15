---
title: Začínáme s Azure App Service Mobile Apps pro aplikace na platformě Xamarin.iOS | Dokumentace Microsoftu
description: V tomto kurzu začnete používat Mobile Apps pro vývoj na platformě Xamarin.iOS.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 8b890630d352619da86c3017426e24f55ef016d9
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27592707"
---
# <a name="create-a-xamarinios-app"></a>Vytvoření aplikace Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Přehled
V tomto kurzu se dozvíte, jak přidat cloudovou back-end službu do mobilní aplikace Xamarin.iOS pomocí back-endu mobilní aplikace Azure.  Vytvoříte nový back-end mobilní aplikace i jednoduchou aplikaci Xamarin.iOS, která bude představovat *seznam úkolů* a bude ukládat data do Azure.

Dokončení tohoto kurzu se předpokládá ve všech dalších kurzech k používání funkce Mobile Apps v Azure App Service pro Xamarin.iOS.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu musí být splněné následující požadavky:

* Aktivní účet Azure. Pokud účet nemáte, můžete si zaregistrovat zkušební verzi Azure a získat až 10 bezplatných mobilních aplikací, které můžete používat i po skončení zkušebního období. Podrobnosti najdete v tématu [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio s Xamarinem. Pokyny najdete v tématu o [nastavení a instalaci pro Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Počítač Mac s nainstalovaným Xcode verze 7.0 nebo novějším a Xamarin Studio Community. Přečtěte si témata o [nastavení a instalaci nástrojů Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) a o [nastavení, instalaci a ověření pro uživatele počítačů Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Vytvoření back-endu mobilní aplikace Azure
Podle těchto pokynů vytvořte back-end mobilní aplikace:

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Konfigurace serverového projektu
Nyní máte zřízen back-end mobilní aplikace Azure, který je možné použít v mobilních klientských aplikacích. Dále si stáhněte serverový projekt pro jednoduchý back-end seznamu úkolů a publikujete ho v Azure.

Podle následujících kroků nakonfigurujte serverový projekt tak, aby používal buď back-end Node.js, nebo .NET.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Stáhnutí a spuštění aplikace Xamarin.iOS
1. Otevřete v okně prohlížeče [Azure Portal].
2. V okně nastavení mobilní aplikace klikněte na **Začínáme** > **Xamarin.iOS**. V kroku 3 klikněte na možnost **Vytvořit novou aplikaci**, pokud ještě nebyla vybrána.  Pak klikněte na tlačítko **Stáhnout**.

      Stáhne se klientská aplikace, která se připojí k mobilnímu back-endu. Uložte komprimovaný soubor projektu do místního počítače a poznamenejte si, kam jste jej uložili.
3. Extrahujte projekt, který jste stáhli, a otevřete jej v nástroji Xamarin Studio (nebo v nástroji Visual Studio).

    ![][9]

    ![][8]
4. Stiskněte klávesu F5, aby se projekt sestavil a aplikace se spustila v emulátoru iPhonu.
5. Zadejte do aplikace smysluplný text, například *Naučit se Xamarin*, a klikněte na tlačítko **+**.

    ![][10]

    Data z požadavku se vloží do tabulky TodoItem. Položky uložené v tabulce se vrátí back-endu mobilní aplikace a v seznamu se zobrazí data.

> [!NOTE]
> Na kód, který přistupuje k back-endu mobilní aplikace pro dotazování a vkládání dat, se můžete podívat v souboru C# QSTodoService.cs.
>
>

## <a name="next-steps"></a>Další kroky
* [Přidání offline synchronizace do aplikace](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Přidání ověřování do aplikace](app-service-mobile-xamarin-ios-get-started-users.md)
* [Přidání nabízených oznámení do aplikace Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Jak používat spravovaného klienta pro Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/
