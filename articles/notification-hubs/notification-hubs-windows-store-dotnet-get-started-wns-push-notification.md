---
title: Zasílání oznámení aplikacím pro Univerzální platformu Windows službou Azure Notification Hubs | Microsoft Docs
description: Osušte se, jak pomocí Centra oznámení Azure nabízená oznámení do aplikace univerzální platformy Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: f78f24ee68545b386169e29a5a52ccc572849ad7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80127062"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Kurz: Odesílání oznámení do aplikací univerzální platformy Windows pomocí center oznámení Azure

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu vytvoříte centrum oznámení, které posílá nabízená oznámení aplikaci pro Univerzální platformu Windows (UPW). Vytvoříte prázdnou aplikaci pro Windows Store, která přijímá nabízená oznámení ze Služby nabízených oznámení Windows (WNS). Potom můžete použít centrum oznámení k vysílání nabízených oznámení do všech zařízení, která běží vaši aplikaci.

> [!NOTE]
> Dokončený kód pro tento kurz najdete na [GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření aplikace ve Windows Storu
> * Vytvoříte centrum oznámení.
> * Vytvoření ukázkové aplikace pro Windows
> * Odeslání zkušebních oznámení

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**. Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet Azure,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Microsoft Visual Studio 2017 nebo novější. Příklad v tomto kurzu používá [Visual Studio 2019](https://www.visualstudio.com/products).
- [Nainstalované nástroje pro vývoj aplikací pro UPW](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Aktivní účet Windows Store
- Zkontrolujte, zda je povoleno **získat oznámení z aplikací a dalších odesílatelů.** 
    - Spusťte okno **Nastavení** v počítači.
    - Vyberte dlaždici **Systém.**
    - V levé nabídce **vyberte Oznámení & akcí.** 
    - Zkontrolujte, zda je povoleno nastavení **Získat oznámení z aplikací a dalších odesílatelů.** Pokud není povolena, povolte ji.

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy služby Notification Hubs pro aplikace UPW.

## <a name="create-an-app-in-windows-store"></a>Vytvoření aplikace ve Windows Storu

Pokud chcete odesílat nabízená oznámení do aplikací UPW, přidružte svou aplikaci k Windows Store. Pak nakonfigurujte centrum oznámení pro integraci se službou WNS.

1. Přejděte na web [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), přihlaste se pod svým účtem Microsoft a vyberte **Vytvořit novou aplikaci**.

    ![Tlačítko nové aplikace](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Zadejte název aplikace a vyberte, že chcete **rezervovat název produktu**. Tím se vytvoří nová registrace Windows Store pro vaši aplikaci.

    ![Uložení názvu aplikace](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Rozbalte **správu produktů**, vyberte **službu WS/MPNS**a pak vyberte **web služeb Live**Services . Přihlaste se ke svému účtu Microsoft. Stránka registrace aplikace se otevře na nové kartě. [My applications](https://apps.dev.microsoft.com)

    ![Stránka WNS/MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Poznamenejte si heslo **tajných kódů aplikací** a **identifikátor zabezpečení balíčku (SID).**

    >[!WARNING]
    >Tajný klíč aplikace a SID balíčku jsou důležité přihlašovací údaje zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací.

## <a name="create-a-notification-hub"></a>Vytvoření centra oznámení

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Konfigurace nastavení WNS centra

1. V kategorii **NASTAVENÍ OZNÁMENÍ** vyberte systém **Windows (WNS).**
2. Zadejte hodnoty **Identifikátor SID balíčku** a **Klíč balíčku**, které jste si poznamenali v předchozí části.
3. Na panelu nástrojů klikněte na tlačítko **Uložit**.

    ![Pole SID balíčku a Klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Vaše centrum oznámení je teď nakonfigurované pro práci se službou WNS. Máte připojovací řetězec, pomocí kterého můžete zaregistrovat aplikaci a odesílat oznámení.

## <a name="create-a-sample-windows-app"></a>Vytvoření ukázkové aplikace pro Windows

1. V Sadě Visual Studio otevřete nabídku **Soubor,** vyberte **Nový**a pak vyberte **Project**.
2. V **dialogovém** okně Vytvořit nový projekt proveďte následující kroky:

    1. Do vyhledávacího pole v horní části zadejte **Windows Universal**.
    2. Ve výsledcích hledání vyberte **Blank App (Universal Windows)** a pak vyberte **Další**.

       ![Dialogové okno Nový projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. V dialogovém **okně Konfigurovat nový projekt** zadejte název **projektu**a **umístění** pro soubory projektu.
    4. Vyberte **Vytvořit**.

3. Potvrďte výchozí hodnoty **cíle**, **minimální** verze platforem a vyberte **OK**.
4. V Průzkumníku řešení klikněte pravým tlačítkem myši na projekt aplikace pro Windows Store, vyberte **Publikovat**a pak vyberte **Přidružit aplikaci ke Storu**. Zobrazí se průvodce **Přidružením aplikace k Windows Store**.
5. V průvodci se přihlaste pomocí svého účtu Microsoft.
6. Vyberte aplikaci zaregistrovanou v kroku 2, vyberte **Další** a pak vyberte **Přidružit**. Tím se přidají požadované informace o registraci Windows Store do manifestu aplikace.
7. V sadě Visual Studio klikněte pravým tlačítkem na řešení a pak vyberte **Spravovat balíčky NuGet**. Otevře se okno **Spravovat balíčky NuGet**.
8. Do vyhledávacího pole zadejte **WindowsAzure.Messaging.Managed**, vyberte **Nainstalovat** a přijměte podmínky použití.

    ![Okno Spravovat balíčky NuGet][20]

    Tato akce stáhne a nainstaluje knihovnu služby Azure Notification Hubs pro Windows a přidá na ni odkaz pomocí [balíčku NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Otevřete `App.xaml.cs` soubor projektu a přidejte následující příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. V `App.xaml.cs` souboru projektu vyhledejte `App` třídu a `InitNotificationsAsync` přidejte následující definici metody. `<your hub name>` Nahraďte název centra oznámení, které jste vytvořili `<Your DefaultListenSharedAccessSignature connection string>` na `DefaultListenSharedAccessSignature` webu Azure Portal, a nahraďte připojovacím řetězcem ze stránky **Access Polices** v centru oznámení:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("<your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

    Tento kód načte identifikátor URI kanálu pro aplikaci z WNS a pak zaregistruje tento kanál URI pomocí centra oznámení.

    >[!NOTE]
    > Nahraďte `hub name` zástupný symbol názvem centra oznámení, které se zobrazí na webu Azure Portal. Zástupný symbol připojovacího řetězce také nahraďte připojovacím `DefaultListenSharedAccessSignature` řetězcem, který jste získali na stránce **Přístupová policie** v centru oznámení v předchozí části.

11. V horní části `OnLaunched` obslužné rutiny události v `App.xaml.cs`přidejte k nové `InitNotificationsAsync` metodě následující volání:

    ```csharp
    InitNotificationsAsync();
    ```

    Tato akce zaručuje, že identifikátor URI kanálu je registrován v centru oznámení při každém spuštění aplikace.

12. Chcete-li aplikaci spustit, stiskněte klávesu **F5** klávesnice. Zobrazí se dialogové okno obsahující registrační klíč. Dialogové okno zavřete klepnutím na tlačítko **OK**.

    ![Úspěšná registrace](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a name="send-test-notifications"></a>Odeslání zkušebních oznámení

Příjem oznámení ve vaší aplikaci můžete rychle otestovat odesláním oznámení na webu [Azure Portal](https://portal.azure.com/).

1. Na webu Azure Portal přepněte na kartu Přehled a na panelu nástrojů vyberte **Poslat na zkoušku**.

    ![Tlačítko Poslat na zkoušku](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. V okně **Poslat na zkoušku** proveďte následující akce:
    1. V položce **Platformy** vyberte **Windows**.
    2. V položce **Typ oznámení** vyberte **Informační zpráva**.
    3. Vyberte **Poslat**.

        ![Podokno Testovací odeslání](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Výsledek odeslání si můžete prohlédnout v seznamu **Výsledek** dole v okně. Vidíte tu také upozornění.

    ![Výsledek operace odeslání](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Na ploše se zobrazí oznámení o **testovací zprávě**.

    ![Oznámení](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Další kroky
Odeslali jste oznámení vysílání do všech zařízení s Windows pomocí portálu nebo konzolové aplikace. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení do konkrétních zařízení](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: https://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: https://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: https://msdn.microsoft.com/library/windows/apps/hh779719.aspx
