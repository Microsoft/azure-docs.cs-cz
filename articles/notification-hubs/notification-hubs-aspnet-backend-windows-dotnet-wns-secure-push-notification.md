---
title: Azure Notification Hubs Secure push pro Windows
description: Naučte se odesílat zabezpečená nabízená oznámení v Azure. Ukázky kódu jsou vytvořeny v C# s použitím .NET API.
author: sethmanheim
manager: femila
editor: thsomasu
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 98e587103e63cd5cc26eab5b00864d00e0b9007f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019412"
---
# <a name="send-secure-push-notifications-from-azure-notification-hubs"></a>Posílání zabezpečených nabízených oznámení z Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

Podpora nabízených oznámení v Microsoft Azure umožňuje přístup ke špičkové infrastruktuře nabízených oznámení s více platformami, která výrazně zjednodušuje implementaci nabízených oznámení pro příjemce i podnikové aplikace pro mobilní platformy.

V důsledku regulativních nebo bezpečnostních omezení někdy může aplikace chtít v oznámení zahrnout něco, co nelze přenést pomocí standardní infrastruktury nabízených oznámení. V tomto kurzu se dozvíte, jak dosáhnout stejného prostředí odesláním citlivých informací prostřednictvím zabezpečeného a ověřeného připojení mezi klientským zařízením a back-endu aplikace.

Tok je na vysoké úrovni následující:

1. Back-end aplikace:
   * Ukládá zabezpečenou datovou část do back-endové databáze.
   * Odešle ID tohoto oznámení do zařízení (nejsou odesílány žádné zabezpečené informace).
2. Aplikace v zařízení při příjmu oznámení:
   * Zařízení kontaktuje back-end požadující zabezpečenou datovou část.
   * Aplikace může datovou část zobrazit jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení po přihlášení uživatele uloží ověřovací token do místního úložiště. To zaručuje zcela bezproblémové prostředí, protože zařízení může načíst zabezpečenou datovou část oznámení pomocí tohoto tokenu. Pokud vaše aplikace neukládá ověřovací tokeny na zařízení nebo pokud tato tokeny vypršela, měla by aplikace zařízení po přijetí oznámení zobrazit obecné oznámení s výzvou, aby uživatel spustil aplikaci. Aplikace pak uživatele ověří a zobrazí datovou část oznámení.

V tomto kurzu se dozvíte, jak bezpečně odeslat nabízené oznámení. Kurz se sestaví v kurzu [informování uživatelů](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) , takže byste nejdřív měli provést kroky v tomto kurzu.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali centrum oznámení, jak je popsáno v tématu [posílání oznámení do aplikací Univerzální platforma Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Všimněte si také, že Windows Phone 8,1 vyžaduje pověření Windows (ne Windows Phone) a že úlohy na pozadí nefungují na Windows Phone 8,0 nebo Silverlight 8,1. Pro aplikace pro Windows Store můžete dostávat oznámení prostřednictvím úlohy na pozadí jenom v případě, že je aplikace zapnutá zamykací obrazovka (klikněte na zaškrtávací políčko v AppManifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Úprava Windows Phoneho projektu

1. V projektu **NotifyUserWindowsPhone** přidejte následující kód do souboru App. XAML. cs a zaregistrujte úlohu nabízených oznámení na pozadí. Přidejte následující řádek kódu na konec metody `OnLaunched()`:

    ```csharp
    RegisterBackgroundTask();
    ```

2. Pořád v souboru App. XAML. cs přidejte následující kód hned za `OnLaunched()` metodu:

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

3. Do `using` horní části souboru App. XAML. cs přidejte následující příkazy:

    ```csharp
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```

4. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Uložit vše**.

## <a name="create-the-push-background-component"></a>Vytvoření komponenty na pozadí push

Dalším krokem je vytvoření komponenty na pozadí push.

1. V Průzkumník řešení klikněte pravým tlačítkem myši na uzel nejvyšší úrovně řešení (**SecurePush řešení** v tomto případě), pak klikněte na **Přidat** a potom na **Nový projekt**.
2. Rozbalte položku **aplikace pro Store**, klikněte na **Windows Phone aplikace** a pak klikněte na **součást prostředí Windows Runtime (Windows Phone)**. Pojmenujte projekt **PushBackgroundComponent** a kliknutím na tlačítko **OK** vytvořte projekt.

    ![Snímek obrazovky dialogového okna Přidat nový projekt se zvýrazněnou možností Visual C# součásti prostředí Windows Runtime (Windows Phone)][12]
3. V Průzkumník řešení klikněte pravým tlačítkem na projekt **PushBackgroundComponent (Windows Phone 8,1)** , pak klikněte na **Přidat** a pak na **Třída**. Pojmenujte novou třídu `PushBackgroundTask.cs` . Pro vygenerování třídy klikněte na tlačítko **Přidat** .
4. Celý obsah `PushBackgroundComponent` definice oboru názvů nahraďte následujícím kódem a nahraďte zástupný symbol koncovým `{back-end endpoint}` bodem back-end, který byl získán při nasazení back-endu:

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

5. V Průzkumník řešení klikněte pravým tlačítkem na projekt **PushBackgroundComponent (Windows Phone 8,1)** a pak klikněte na **Spravovat balíčky NuGet**.
6. Na levé straně klikněte na **Online**.
7. Do pole **Hledat** zadejte **Http Client**.
8. V seznamu výsledků klikněte na **klientské knihovny Microsoft http** a pak klikněte na **nainstalovat**. Dokončete instalaci.
9. Vraťte se do pole **Hledat** pro balíčky NuGet a zadejte **Json.net**. Nainstalujte balíček **JSON.NET** a pak zavřete okno Správce balíčků NuGet.
10. `using`Na začátek souboru přidejte následující příkazy `PushBackgroundTask.cs` :

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

11. V Průzkumník řešení projektu **NotifyUserWindowsPhone (Windows Phone 8,1)** klikněte pravým tlačítkem na **odkazy** a pak klikněte na **Přidat odkaz...**. V dialogovém okně Správce odkazů zaškrtněte políčko vedle **PushBackgroundComponent** a pak klikněte na **OK**.
12. V Průzkumník řešení dvakrát klikněte na **Package. appxmanifest** v projektu **NotifyUserWindowsPhone (Windows Phone 8,1)** . V části **oznámení** nastavte **informační** zprávy s možností **Ano**.

    ![Snímek obrazovky okna Průzkumník řešení se zaměřením na balíček. appxmanifest s možností možnosti informační zprávy nastavenou na hodnotu Ano, která je červená.][3]
13. Pořád v **balíčku. appxmanifest** klikněte na nabídku **deklarace** v horní části. V rozevíracím seznamu **dostupné deklarace** klikněte na **úlohy na pozadí** a pak klikněte na **Přidat**.
14. V **balíčku. appxmanifest** v části **vlastnosti** zaškrtněte **nabízené oznámení**.
15. V **balíčku. appxmanifest** v části **nastavení aplikace** zadejte do pole **vstupní bod** **PushBackgroundComponent. PushBackgroundTask** .

    ![Snímek obrazovky okna Průzkumník řešení se zaměřením na balíček. appxmanifest pomocí dostupných deklarací, podporovaných deklarací, nabízených oznámení a možností vstupního bodu popsaných červeně.][13]
16. V nabídce **Soubor** klikněte na **Uložit vše**.

## <a name="run-the-application"></a>Spuštění aplikace

Chcete-li spustit aplikaci, postupujte následovně:

1. V aplikaci Visual Studio spusťte aplikaci webového rozhraní API **projekt appbackend** . Zobrazí se webová stránka ASP.NET.
2. V aplikaci Visual Studio spusťte aplikaci Windows Phone **NotifyUserWindowsPhone (Windows Phone 8,1)** . Emulátor Windows Phone se spustí a automaticky načte aplikaci.
3. V uživatelském rozhraní aplikace **NotifyUserWindowsPhone** zadejte uživatelské jméno a heslo. Může se jednat o libovolný řetězec, ale musí se jednat o stejnou hodnotu.
4. V uživatelském rozhraní aplikace **NotifyUserWindowsPhone** klikněte na **Přihlásit se a zaregistrujte** se. Pak klikněte na **Odeslat nabízení**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
