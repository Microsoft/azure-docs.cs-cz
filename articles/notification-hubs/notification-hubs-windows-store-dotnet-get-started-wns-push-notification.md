---
title: Zasílání oznámení aplikacím pro Univerzální platformu Windows službou Azure Notification Hubs | Microsoft Docs
description: Naučte se používat Azure Notification Hubs k nabízeným oznámením do aplikace Univerzální platformy Windows.
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: 'mvc, ms.custom: devx-track-csharp'
ms.date: 12/05/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 12/04/2019
ms.openlocfilehash: 4f55b6eafe230f722979d535111ce45aa35981f0
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93125033"
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-using-azure-notification-hubs"></a>Kurz: odesílání oznámení do Univerzální platforma Windows aplikací pomocí Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu vytvoříte centrum oznámení, které posílá nabízená oznámení aplikaci pro Univerzální platformu Windows (UPW). Vytvoříte prázdnou aplikaci pro Windows Store, která přijímá nabízená oznámení ze Služby nabízených oznámení Windows (WNS). Pak použijete centrum oznámení k vysílání nabízených oznámení na všechna zařízení, na kterých běží vaše aplikace.

> [!NOTE]
> Dokončený kód pro tento kurz najdete na [GitHubu](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/UwpSample).

Proveďte následující kroky:

> [!div class="checklist"]
> * Vytvoření aplikace ve Windows Storu
> * Vytvoříte centrum oznámení.
> * Vytvoření ukázkové aplikace pro Windows
> * Odeslání zkušebních oznámení

## <a name="prerequisites"></a>Předpoklady

- **Předplatné Azure** . Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Microsoft Visual Studio 2017 nebo novější. V příkladu v tomto kurzu se používá [Visual Studio 2019](https://www.visualstudio.com/products).
- [Nainstalované nástroje pro vývoj aplikací pro UPW](/windows/uwp/get-started/get-set-up)
- Aktivní účet Windows Store
- Potvrďte, že je povolené nastavení **dostávat oznámení z aplikací a další odesílatelé** . 
    - V počítači spusťte okno **Nastavení** .
    - Vyberte dlaždici **systému** .
    - V nabídce vlevo vyberte **oznámení & akce** . 
    - Ověřte, že je povolené nastavení **získat oznámení z aplikací a další odesílatelé** . Pokud není povolená, povolte ji.

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy služby Notification Hubs pro aplikace UPW.

## <a name="create-an-app-in-windows-store"></a>Vytvoření aplikace ve Windows Storu

Pokud chcete odesílat nabízená oznámení do aplikací UPW, přidružte svou aplikaci k Windows Store. Pak nakonfigurujte centrum oznámení pro integraci se službou WNS.

1. Přejděte na web [Windows Dev Center](https://partner.microsoft.com/dashboard/windows/first-run-experience), přihlaste se pod svým účtem Microsoft a vyberte **Vytvořit novou aplikaci** .

    ![Tlačítko nové aplikace](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
2. Zadejte název aplikace a vyberte, že chcete **rezervovat název produktu** . Tím se vytvoří nová registrace Windows Store pro vaši aplikaci.

    ![Uložení názvu aplikace](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
3. Rozbalte položku **Správa produktů** , vyberte **WNS/MPNS** a pak vyberte **web služby Live Services** . Přihlaste se ke svému účtu Microsoft. Na nové kartě se otevře stránka pro registraci aplikace. Alternativně můžete přejít přímo na stránku [Moje aplikace](https://apps.dev.microsoft.com) a vybrat název aplikace, který se zobrazí na této stránce.

    ![Stránka WNS/MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
4. Všimněte si hesla pro **tajné klíče aplikace** i **identifikátoru zabezpečení (SID) balíčku** i **identity aplikace** v části Windows Store.

    >[!WARNING]
    >Tajný klíč aplikace a SID balíčku jsou důležité přihlašovací údaje zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací.

## <a name="create-a-notification-hub"></a>Vytvoření centra oznámení

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-wns-settings-for-the-hub"></a>Konfigurace nastavení WNS centra

1. V kategorii **Nastavení oznámení** vyberte možnost **Windows (WNS)** .
2. Zadejte hodnoty **Identifikátor SID balíčku** a **Klíč balíčku** , které jste si poznamenali v předchozí části.
3. Na panelu nástrojů klikněte na tlačítko **Uložit** .

    ![Pole SID balíčku a Klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Vaše centrum oznámení je teď nakonfigurované pro práci se službou WNS. Máte připojovací řetězec, pomocí kterého můžete zaregistrovat aplikaci a odesílat oznámení.

## <a name="create-a-sample-windows-app"></a>Vytvoření ukázkové aplikace pro Windows

1. V aplikaci Visual Studio otevřete nabídku **soubor** , vyberte možnost **Nový** a pak vyberte možnost **projekt** .
2. V dialogovém okně **vytvořit nový projekt** proveďte následující kroky:

    1. Do vyhledávacího pole v horní části zadejte **Windows Universal** .
    2. Ve výsledcích hledání vyberte **prázdná aplikace (univerzální pro Windows)** a pak vyberte **Další** .

       ![Dialogové okno Nový projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)

    3. V dialogovém okně **Konfigurace nového projektu** zadejte **název projektu** a **umístění** souborů projektu.
    4. Vyberte **Vytvořit** .

3. Potvrďte výchozí hodnoty **cíle** , **minimální** verze platforem a vyberte **OK** .
4. V Průzkumník řešení klikněte pravým tlačítkem na projekt aplikace pro Windows Store, vyberte **publikovat** a pak vyberte **přidružit aplikaci ke Storu** . Zobrazí se průvodce **Přidružením aplikace k Windows Store** .
5. V průvodci se přihlaste pomocí svého účtu Microsoft.
6. Vyberte aplikaci zaregistrovanou v kroku 2, vyberte **Další** a pak vyberte **Přidružit** . Tím se přidají požadované informace o registraci Windows Store do manifestu aplikace.
7. V sadě Visual Studio klikněte pravým tlačítkem na řešení a pak vyberte **Spravovat balíčky NuGet** . Otevře se okno **Spravovat balíčky NuGet** .
8. Do vyhledávacího pole zadejte **WindowsAzure.Messaging.Managed** , vyberte **Nainstalovat** a přijměte podmínky použití.

    ![Okno Spravovat balíčky NuGet][20]

    Tato akce stáhne a nainstaluje knihovnu služby Azure Notification Hubs pro Windows a přidá na ni odkaz pomocí [balíčku NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).
9. Otevřete `App.xaml.cs` soubor projektu a přidejte následující příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.UI.Popups;
    ```

10. V `App.xaml.cs` souboru projektu vyhledejte `App` třídu a přidejte následující `InitNotificationsAsync` definici metody. Nahraďte `<your hub name>` názvem centra oznámení, které jste vytvořili v Azure Portal, a nahraďte `<Your DefaultListenSharedAccessSignature connection string>` `DefaultListenSharedAccessSignature` připojovacím řetězcem na stránce **Policies (zásady přístupu** ) centra oznámení:

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
    > `hub name`Zástupný symbol nahraďte názvem centra oznámení, který se zobrazí v Azure Portal. Také nahraďte zástupný symbol připojovacího řetězce `DefaultListenSharedAccessSignature` připojovacím řetězcem, který jste získali ze stránky **zásady přístupu** vašeho centra oznámení v předchozí části.

11. V horní části `OnLaunched` obslužné rutiny události v `App.xaml.cs` přidejte následující volání do nové `InitNotificationsAsync` metody:

    ```csharp
    InitNotificationsAsync();
    ```

    Tato akce zaručuje, že identifikátor URI kanálu je zaregistrován v centru oznámení pokaždé, když se aplikace spustí.

12. Klikněte pravým tlačítkem na `Package.appxmanifest` a vyberte Zobrazit kód ( **F7** ). Vyhledejte  `<Identity .../>` a nahraďte hodnotu **identitou aplikace** z WNS, kterou jste vytvořili [dříve](#create-an-app-in-windows-store).

13. Pokud chcete aplikaci spustit, stiskněte klávesu **F5** klávesnice. Zobrazí se dialogové okno obsahující registrační klíč. Chcete-li zavřít dialogové okno, klikněte na tlačítko **OK** .

    ![Úspěšná registrace](./media/notification-hubs-windows-store-dotnet-get-started/registration-successful.png)

Vaše aplikace je teď připravena přijímat oznámení informačního nápisu.

## <a name="send-test-notifications"></a>Odeslání zkušebních oznámení

Příjem oznámení ve vaší aplikaci můžete rychle otestovat odesláním oznámení na webu [Azure Portal](https://portal.azure.com/).

1. Na webu Azure Portal přepněte na kartu Přehled a na panelu nástrojů vyberte **Poslat na zkoušku** .

    ![Tlačítko Poslat na zkoušku](./media/notification-hubs-windows-store-dotnet-get-started/test-send-button.png)
2. V okně **Poslat na zkoušku** proveďte následující akce:
    1. V položce **Platformy** vyberte **Windows** .
    2. V položce **Typ oznámení** vyberte **Informační zpráva** .
    3. Vyberte **Poslat** .

        ![Podokno Testovací odeslání](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)
3. Výsledek odeslání si můžete prohlédnout v seznamu **Výsledek** dole v okně. Vidíte tu také upozornění.

    ![Výsledek operace odeslání](./media/notification-hubs-windows-store-dotnet-get-started/result-of-send.png)
4. Na ploše se zobrazí oznámení o **testovací zprávě** .

    ![Oznámení](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)

## <a name="next-steps"></a>Další kroky
Poslali jste oznámení všesměrového vysílání na všechna vaše zařízení s Windows pomocí portálu nebo aplikace konzoly. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem:

> [!div class="nextstepaction"]
>[Nabízená oznámení odesílaná konkrétním zařízením](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[toast catalog]: /previous-versions/windows/apps/hh761494(v=win.10)
[tile catalog]: /previous-versions/windows/apps/hh761491(v=win.10)
[badge overview]: /previous-versions/windows/apps/hh779719(v=win.10)
