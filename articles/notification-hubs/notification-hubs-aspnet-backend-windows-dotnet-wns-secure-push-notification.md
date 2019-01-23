---
title: Azure Notification Hubs zabezpečené nabízená oznámení
description: Zjistěte, jak k odesílání zabezpečených nabízených oznámení v Azure. Ukázky kódu jsou vytvořeny v C# s použitím .NET API.
documentationcenter: windows
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: cf23ef5df3bdcaad23841da111fa06cc36b4cd57
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475675"
---
# <a name="securely-push-notifications-from-azure-notification-hubs"></a>Bezpečně nabízená oznámení ze služby Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Přehled

Podpora nabízená oznámení v Microsoft Azure umožňuje získat přístup k snadným ovládáním, multiplatformní a horizontálním navýšením kapacity škálovanou infrastrukturu, což výrazně zjednodušuje provádění nabízená oznámení pro zákaznické a podnikové aplikace pro mobilní zařízení platformy.

Z důvodu dodržování legislativních nebo omezení zabezpečení, někdy aplikace může být vhodné zahrnout něco oznámení, který nemůže být přenesen prostřednictvím infrastrukturu standardní nabízených oznámení. Tento kurz popisuje, jak dosáhnout stejné prostředí tím, že odesílání citlivé informace přes zabezpečené ověřené připojení mezi klientským zařízením a back-endu aplikace.

Na vysoké úrovni tok je následujícím způsobem:

1. Back-end aplikace:
   * Úložiště zabezpečené datové části v back-end databáze.
   * ID tohoto oznámení se odešle do zařízení (se neodesílají žádné informace o zabezpečení).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení kontaktuje back endu, zabezpečené datové části požadavku.
   * Aplikace můžete zobrazit datovou část jako oznámení na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token v místním úložišti, po přihlášení uživatele. Zaručí se tak zcela hladce, jak zařízení můžete načíst zabezpečené pomocí tohoto tokenu datovou část v oznámení. Pokud vaše aplikace neukládá ověřovacích tokenů na zařízení nebo pokud můžete platnost těchto tokenů, aplikace pro zařízení, při přijetí oznámení by měl zobrazit obecné oznámení výzvou, aby uživatel ke spuštění aplikace. Aplikace pak ověří uživatele a zobrazuje datová část oznámení.

V tomto kurzu zabezpečení nabízené ukazuje, jak bezpečně pošle nabízené oznámení. Tento kurz vychází [oznamování uživatelům pomocí](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) kurz, abyste měli dokončíte kroky uvedené v tomto kurzu nejprve.

> [!NOTE]
> Tento kurz předpokládá, že jste vytvořili a konfiguraci centra oznámení, jak je popsáno v [Začínáme se službou Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Všimněte si také, že Windows Phone 8.1 vyžaduje přihlašovací údaje Windows (ne Windows Phone) a úlohy na pozadí se nefungují na Windows Phone 8.0 nebo Silverlight 8.1. Pro aplikace Windows Store, můžete přijímat oznámení prostřednictvím úlohy na pozadí pouze v případě, že aplikace je povolená zamykací obrazovky (klikněte na zaškrtávací políčko v Appmanifest).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Upravit projekt Windows Phone

1. V **NotifyUserWindowsPhone** projektu, přidejte následující kód do souboru App.xaml.cs registrace nabízených oznámení úlohy na pozadí. Přidejte následující řádek kódu na konec metody `OnLaunched()`:

    ```c#
    RegisterBackgroundTask();
    ```
2. Stále v souboru App.xaml.cs přidejte následující kód bezprostředně po `OnLaunched()` metody:

    ```c#
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
3. Přidejte následující `using` příkazů v horní části souboru App.xaml.cs:

    ```c#
    using Windows.Networking.PushNotifications;
    using Windows.ApplicationModel.Background;
    ```
4. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Uložit vše**.

## <a name="create-the-push-background-component"></a>Vytvoření komponenty na pozadí nabízených oznámení

Dalším krokem je vytvoření komponenty na pozadí nabízených oznámení.

1. V Průzkumníku řešení klikněte pravým tlačítkem myši na uzel řešení na nejvyšší úrovni (**řešení SecurePush** v tomto případě), potom klikněte na tlačítko **přidat**, klikněte na **nový projekt**.
2. Rozbalte **Store aplikace**, pak klikněte na tlačítko **aplikace Windows Phone**, klikněte na **součást prostředí Windows Runtime (Windows Phone)**. Pojmenujte projekt **PushBackgroundComponent**a potom klikněte na tlačítko **OK** pro vytvoření projektu.

    ![][12]
3. V Průzkumníku řešení klikněte pravým tlačítkem myši **PushBackgroundComponent (Windows Phone 8.1)** projektu a pak klikněte na **přidat**, klikněte na **třídy**. Pojmenujte novou třídu `PushBackgroundTask.cs`. Klikněte na tlačítko **přidat** ke generování třídy.
4. Nahradí celý obsah `PushBackgroundComponent` definice oboru názvů následujícím kódem, kde nahradíte zástupný text `{back-end endpoint}` s koncovým bodem back-end získané při nasazování back endu:

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
5. V Průzkumníku řešení klikněte pravým tlačítkem myši **PushBackgroundComponent (Windows Phone 8.1)** projektu a pak klikněte na tlačítko **spravovat balíčky NuGet**.
6. Na levé straně klikněte na **Online**.
7. Do pole **Hledat** zadejte **Http Client**.
8. V seznamu výsledků klepněte na tlačítko **knihovny klienta HTTP Microsoft**a potom klikněte na tlačítko **nainstalovat**. Dokončete instalaci.
9. Vraťte se do pole **Hledat** pro balíčky NuGet a zadejte **Json.net**. Nainstalujte **Json.NET** balíček a potom zavřete okno Správce balíčků NuGet.
10. Přidejte následující `using` příkazů v horní části `PushBackgroundTask.cs` souboru:

    ```c#
    using Windows.ApplicationModel.Background;
    using Windows.Networking.PushNotifications;
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using Windows.UI.Notifications;
    using Windows.Data.Xml.Dom;
    ```
11. V Průzkumníku řešení v **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu, klikněte pravým tlačítkem na **odkazy**, pak klikněte na tlačítko **přidat odkaz...** . V dialogovém okně Správce odkazů, zaškrtněte políčko vedle položky **PushBackgroundComponent**a potom klikněte na tlačítko **OK**.
12. V Průzkumníku řešení poklikejte na **Package.appxmanifest** v **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu. V části **oznámení**, nastavte **podporuje informační zprávy** k **Ano**.

    ![][3]
13. Pořád ještě v **Package.appxmanifest**, klikněte na tlačítko **deklarace** nabídce v horní části. V **dostupné deklarace** rozevírací seznam, klikněte na tlačítko **úlohy na pozadí**a potom klikněte na tlačítko **přidat**.
14. V **Package.appxmanifest**v části **vlastnosti**, zkontrolujte **nabízené oznámení**.
15. V **Package.appxmanifest**v části **nastavení aplikace**, typ **PushBackgroundComponent.PushBackgroundTask** v **vstupní bod** pole.

    ![][13]
16. V nabídce **Soubor** klikněte na **Uložit vše**.

## <a name="run-the-application"></a>Spuštění aplikace

Pokud chcete spustit aplikaci, postupujte takto:

1. V sadě Visual Studio, spusťte **AppBackend** aplikace webového rozhraní API. Zobrazí se webová stránka ASP.NET.
2. V sadě Visual Studio, spusťte **NotifyUserWindowsPhone (Windows Phone 8.1)** aplikace Windows Phone. Emulátor Windows Phone spuštěn a automaticky načte aplikaci.
3. V **NotifyUserWindowsPhone** aplikace uživatelského rozhraní, zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
4. V **NotifyUserWindowsPhone** aplikace uživatelského rozhraní, klikněte na tlačítko **přihlášení a registraci**. Pak klikněte na tlačítko **odesílání nabízených oznámení**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
