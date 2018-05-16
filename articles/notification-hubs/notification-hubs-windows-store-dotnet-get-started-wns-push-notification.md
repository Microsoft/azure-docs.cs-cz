---
title: Zasílání oznámení aplikacím pro Univerzální platformu Windows službou Azure Notification Hubs | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí služby Azure Notification Hubs odesílat nabízená oznámení do aplikace Univerzální platformy Windows.
services: notification-hubs
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: c3bb170800508d5a546573850f445b2a8991ea8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-send-notifications-to-universal-windows-platform-apps-by-using-azure-notification-hubs"></a>Kurz: Zasílání oznámení aplikacím pro Univerzální platformu Windows službou Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

V tomto kurzu vytvoříte centrum oznámení, které posílá nabízená oznámení aplikaci pro Univerzální platformu Windows (UPW). Vytvoříte prázdnou aplikaci pro Windows Store, která přijímá nabízená oznámení ze Služby nabízených oznámení Windows (WNS). Potom můžete použít centrum oznámení k vysílání nabízených oznámení do všech zařízení, na kterých běží vaše aplikace.

> [!NOTE]
> Dokončený kód pro tento kurz najdete na [GitHubu](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoříte aplikaci ve Windows Storu
> * Vytvoříte centrum oznámení
> * Vytvoříte ukázkovou aplikaci pro Windows
> * Odešlete zkušební oznámení


## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) nebo novější
- [Nainstalované nástroje pro vývoj aplikací pro UPW](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
- Aktivní účet Windows Store

Dokončení tohoto kurzu je předpokladem pro všechny ostatní kurzy služby Notification Hubs pro aplikace UPW.

## <a name="create-an-app-in-windows-store"></a>Vytvoření aplikace ve Windows Storu
Pokud chcete odesílat nabízená oznámení do aplikací UPW, přidružte svou aplikaci k Windows Store. Pak nakonfigurujte centrum oznámení pro integraci se službou WNS.

1. Přejděte na web [Windows Dev Center](https://dev.windows.com/overview), přihlaste se pod svým účtem Microsoft a vyberte **Vytvořit novou aplikaci**.

    ![Tlačítko nové aplikace](./media/notification-hubs-windows-store-dotnet-get-started/windows-store-new-app-button.png)
1. Zadejte název aplikace a vyberte, že chcete **rezervovat název produktu**. Tím se vytvoří nová registrace Windows Store pro vaši aplikaci.

    ![Uložení názvu aplikace](./media/notification-hubs-windows-store-dotnet-get-started/store-app-name.png)
1. Rozbalte **Správu aplikací**, vyberte **WNS/MPNS**, vyberte **WNS/MPNS**, a pak vyberte **web Live Services**. Přihlaste se ke svému účtu Microsoft. **Portál pro registraci aplikací** se otevře na nové kartě. Na stránku [portálu pro registraci aplikací](http://apps.dev.microsoft.com) také můžete přejít přímo, když vyberete název aplikace.

    ![Stránka WNS/MPNS](./media/notification-hubs-windows-store-dotnet-get-started/wns-mpns-page.png)
1.   Poznamenejte si **tajný klíč aplikace** a **identifikátor zabezpečení (SID) balíčku**.

        >[!WARNING]
        >Tajný klíč aplikace a SID balíčku jsou důležité přihlašovací údaje zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s vaší aplikací.

## <a name="create-a-notification-hub"></a>Vytvoření centra oznámení
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


### <a name="configure-wns-settings-for-the-hub"></a>Konfigurace nastavení WNS centra

1. V kategorii **NASTAVENÍ OZNÁMENÍ** vyberte **Windows (WNS)**. 
2. Zadejte hodnoty **Identifikátor SID balíčku** a **Klíč balíčku**, které jste si poznamenali v předchozí části. 
3. Na panelu nástrojů vyberte **Uložit**.

    ![Pole SID balíčku a Klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

Vaše centrum oznámení je teď nakonfigurované pro práci se službou WNS. Máte připojovací řetězec, pomocí kterého můžete zaregistrovat aplikaci a odesílat oznámení.

## <a name="create-a-sample-windows-app"></a>Vytvoření ukázkové aplikace pro Windows
1. V sadě Visual Studio vyberte **Soubor**, přejděte na **Nový** a vyberte **Projekt**. 
2. V dialogovém okně **Nový projekt** proveďte následující kroky: 

    1. Rozbalte **Visual C#**.
    2. Vyberte **Univerzální aplikace pro Windows**. 
    3. Vyberte **Prázdná aplikace (univerzální pro Windows)**. 
    4. Zadejte **název** projektu. 
    5. Vyberte **OK**. 

        ![Dialogové okno Nový projekt](./media/notification-hubs-windows-store-dotnet-get-started/new-project-dialog.png)
1. Potvrďte výchozí hodnoty **cíle**, **minimální** verze platforem a vyberte **OK**. 
2. V Průzkumníku řešení klikněte pravým tlačítkem na projekt aplikace pro Windows Store, vyberte **Store** a pak vyberte **Přidružit aplikaci ve Store**. Zobrazí se průvodce **Přidružením aplikace k Windows Store**.
3. V průvodci se přihlaste pomocí svého účtu Microsoft.
4. Vyberte aplikaci zaregistrovanou v kroku 2, vyberte **Další** a pak vyberte **Přidružit**. Tím se přidají požadované informace o registraci Windows Store do manifestu aplikace.
5. V sadě Visual Studio klikněte pravým tlačítkem na řešení a pak vyberte **Spravovat balíčky NuGet**. Otevře se okno **Spravovat balíčky NuGet**.
6. Do vyhledávacího pole zadejte **WindowsAzure.Messaging.Managed**, vyberte **Nainstalovat** a přijměte podmínky použití.
   
    ![Okno Spravovat balíčky NuGet][20]
   
    Tato akce stáhne a nainstaluje knihovnu služby Azure Notification Hubs pro Windows a přidá na ni odkaz pomocí [balíčku NuGet Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs).

3. Otevřete soubor projektu App.xaml.cs a přidejte následující příkazy `using`: 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

4. V souboru App.xaml.cs také přidejte do třídy **App** následující definici metody **InitNotificationsAsync**:
   
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
   
    Tento kód načte identifikátor URI kanálu pro aplikaci z WNS a pak zaregistruje tento kanál URI pomocí centra oznámení.
   
    >[!NOTE]
    >* Zástupný text **hub name** nahraďte názvem centra oznámení, který se zobrazí na webu Azure Portal. 
    >* Nahraďte také zástupný připojovací řetězec připojovacím řetězcem **DefaultListenSharedAccessSignature**, který jste získali v předchozí části na stránce **Zásady přístupu** vašeho centra oznámení.
   > 
   > 
5. V horní části **OnLaunched** obslužné rutiny událostí v souboru App.xaml.cs přidejte následující volání do nové metody **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    Tato akce zaručuje, že se identifikátor URI kanálu zaregistruje v centru oznámení při každém spuštění aplikace.

6. Pokud chcete aplikaci spustit, stiskněte klávesu **F5**. Zobrazí se dialogové okno, které obsahuje registrační klíč. Zavřete dialogové okno volbou **OK**. 

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
1. Na ploše se zobrazí oznámení o **testovací zprávě**. 

    ![Oznámení](./media/notification-hubs-windows-store-dotnet-get-started/test-notification-message.png)


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste z portálu nebo konzolové aplikace odeslali vysílané oznámení všem zařízením s Windows. Pokud se chcete naučit posílat nabízená oznámení jenom určitým zařízením, pokračujte následujícím kurzem: 

> [!div class="nextstepaction"]
>[Zasílání nabízených oznámení určitým zařízením](
notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)


<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[toast catalog]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[tile catalog]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[badge overview]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
 
