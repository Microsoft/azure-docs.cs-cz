---
title: Azure Notification Hubs Secure Push pro Windows
description: Přečtěte si, jak v Azure odesílat zabezpečená nabízená oznámení. Ukázky kódu jsou vytvořeny v C# s použitím .NET API.
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: db42cf7f886855af77073963e6f04ac088ca5612
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530727"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Bezpečně nabízená oznámení z Centra oznámení Azure

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup ke snadno použitelné víceplatformové a škálované nabízené infrastruktuře, což výrazně zjednodušuje implementaci nabízených oznámení pro spotřebitelské i podnikové aplikace pro mobilní zařízení. Platformy.

Z důvodu regulačních nebo bezpečnostních omezení může aplikace někdy chtít zahrnout něco do oznámení, které nelze přenášet prostřednictvím standardní infrastruktury nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného, ověřeného připojení mezi klientským zařízením a back-endem aplikace.

Na vysoké úrovni je tok následující:

1. Back-end aplikace:
   * Ukládá zabezpečené datové části v back-end databázi.
   * Odešle ID tohoto oznámení do zařízení (nejsou odesílány žádné zabezpečené informace).
2. Aplikace na zařízení při příjmu oznámení:
   * Zařízení kontaktuje back-end požadující zabezpečenou datovou část.
   * Aplikace může zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token v místním úložišti po přihlášení uživatele. To zaručuje zcela bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tyto tokeny mohou vypršet, aplikace zařízení po obdržení oznámení by měla zobrazit obecné oznámení s výzvou uživateli ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazí datovou část oznámení.

Tento kurz zabezpečeného nabízení ukazuje, jak bezpečně odeslat nabízené oznámení. Kurz vychází z kurzu [Upozornit uživatele,](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) takže byste měli nejprve provést kroky v tomto kurzu.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v [části Začínáme s centry oznámení (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Všimněte si také, že Windows Phone 8.1 vyžaduje přihlašovací údaje systému Windows (nikoli Windows Phone) a že úlohy na pozadí nefungují ve Windows Phone 8.0 nebo Silverlight 8.1. U aplikací pro Windows Store můžete přijímat oznámení prostřednictvím úlohy na pozadí pouze v případě, že je aplikace povolena pro zamykací obrazovku (klikněte na zaškrtávací políčko v manifestu.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Úprava projektu Windows Phone

1. V projektu **NotifyUserWindowsPhone** přidejte do App.xaml.cs následující kód pro registraci úkolu push na pozadí. Přidejte následující řádek kódu na konec metody `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```
2. Stále v App.xaml.cs, přidejte následující `OnLaunched()` kód bezprostředně za metodu:

    ```csharp
    private async void RegisterBackgroundTask()
    {
        if (!Windows.ApplicationModel.Background.BackgroundTaskRegistration.AllTasks.Any(i => i.Value.Name == "PushBackgroundTask"))
        {
            var result = await BackgroundExecutionManager.RequestAccessAsync();
            var builder = new BackgroundTaskBuilder();

            builder.Name = "PushBackgroundTask";
            builder.TaskEntryPoint = typeof(PushBackgroundComponent.PushBackgroundTask).FullName;
            builder.SetTrigger(new Windows.ApplicationModel.Background.PushNotificationTrigger());
            BackgroundTaskRegistration task = builder.Register();
        }
    }
    ```
3. V horní `using` části souboru App.xaml.cs přidejte následující příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Uložit vše**.

## <a name="create-the-push-background-component"></a>Vytvoření komponenty Push pozadí

Dalším krokem je vytvoření součásti push pozadí.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na uzel nejvyšší úrovně řešení **(řešení SecurePush** v tomto případě), potom klikněte na **Přidat**a potom klikněte na **Nový projekt**.
2. Rozbalte **položku Store Apps**, potom klikněte na **Položky Windows Phone Apps**a potom klikněte na součást Windows **Runtime (Windows Phone).** Pojmenujte projekt **PushBackgroundComponent**a potom klepnutím na **tlačítko OK** projekt vytvořte.

    ![][12]
3. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **PushBackgroundComponent (Windows Phone 8.1)** a potom klepněte na příkaz **Přidat**a potom klepněte na příkaz **Třída**. Pojmenujte `PushBackgroundTask.cs`novou třídu . Kliknutím na **Přidat** vygenerujte třídu.
4. Nahraďte celý `PushBackgroundComponent` obsah definice oboru názvů následujícím kódem a nahraďte zástupný symbol `{back-end endpoint}` koncovým bodem back-end získaným při nasazování back-endu:

    ```csharp
    public sealed class Notification
        {
            public int Id { get; set; }
            public string Payload { get; set; }
            public bool Read { get; set; }
        }

        public sealed class PushBackgroundTask : IBackgroundTask
        {
            private string GET_URL = "{back-end endpoint}/api/notifications/";

            async void IBackgroundTask.Run(IBackgroundTaskInstance taskInstance)
            {
                // Store the content received from the notification so it can be retrieved from the UI.
                RawNotification raw = (RawNotification)taskInstance.TriggerDetails;
                var notificationId = raw.Content;

                // retrieve content
                BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
                var httpClient = new HttpClient();
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                var notificationString = await httpClient.GetStringAsync(GET_URL + notificationId);

                var notification = JsonConvert.DeserializeObject<Notification>(notificationString);

                ShowToast(notification);

                deferral.Complete();
            }

            private void ShowToast(Notification notification)
            {
                ToastTemplateType toastTemplate = ToastTemplateType.ToastText01;
                XmlDocument toastXml = ToastNotificationManager.GetTemplateContent(toastTemplate);
                XmlNodeList toastTextElements = toastXml.GetElementsByTagName("text");
                toastTextElements[0].AppendChild(toastXml.CreateTextNode(notification.Payload));
                ToastNotification toast = new ToastNotification(toastXml);
                ToastNotificationManager.CreateToastNotifier().Show(toast);
            }
        }
    ```
5. V Průzkumníku řešení klepněte pravým tlačítkem myši na projekt **PushBackgroundComponent (Windows Phone 8.1)** a potom klepněte na příkaz **Spravovat balíčky NuGet**.
6. Na levé straně klikněte na **Online**.
7. Do pole **Hledat** zadejte **Http Client**.
8. V seznamu výsledků klepněte na **položku Klientské knihovny MICROSOFT HTTP**a potom klepněte na tlačítko **Instalovat**. Dokončete instalaci.
9. Vraťte se do pole **Hledat** pro balíčky NuGet a zadejte **Json.net**. Nainstalujte **Json.NET** balíček a zavřete okno Správce balíčků NuGet.
10. V horní `using` části souboru `PushBackgroundTask.cs` přidejte následující příkazy:

    ```csharp
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. V Průzkumníku řešení v projektu **NotifyUserWindowsPhone (Windows Phone 8.1)** klepněte pravým tlačítkem myši na **položku Reference**a potom klepněte na příkaz **Přidat odkaz...**. V dialogovém okně Správce odkazů zaškrtněte políčko vedle **položky PushBackgroundComponent**a klepněte na tlačítko **OK**.
12. V Průzkumníku řešení poklikejte na **Package.appxmanifest** v projektu **NotifyUserWindowsPhone (Windows Phone 8.1).** V části **Oznámení**nastavte **informační zprávu schopnou** na **ano**.

    ![][3]
13. Stále v **Package.appxmanifest**, klepněte na nabídku **Prohlášení** v horní části. V rozevíracím seznamu **Dostupná prohlášení** klikněte na **Úkoly na pozadí**a potom klikněte na **Přidat**.
14. V **Package.appxmanifest**, v části **Vlastnosti**, zkontrolujte **Push oznámení**.
15. V **souboru Package.appxmanifest**zadejte v části **Nastavení aplikace**do pole Vstupní **bod** **příkaz PushBackgroundComponent.PushBackgroundTask.**

    ![][13]
16. V nabídce **Soubor** klikněte na **Uložit vše**.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li aplikaci spustit, postupujte takto:

1. V sadě Visual Studio spusťte aplikaci **AppBackend** Web API. Zobrazí se ASP.NET webová stránka.
2. V sadě Visual Studio spusťte aplikaci **NotifyUserWindowsPhone (Windows Phone 8.1)** pro Windows Phone. Emulátor Windows Phone spustí a automaticky načte aplikaci.
3. V uživatelském rozhraní aplikace **NotifyUserWindowsPhone** zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí mít stejnou hodnotu.
4. V uživatelském rozhraní aplikace **NotifyUserWindowsPhone** klepněte na tlačítko **Přihlásit se a zaregistrovat**. Potom klepněte na **tlačítko Odeslat nabízenou položku**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
