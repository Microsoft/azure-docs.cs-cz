---
title: Azure Notification Hubs zabezpečené Push
description: Zjistěte, jak odeslat zabezpečené nabízená oznámení v Azure. Ukázky kódu jsou vytvořeny v C# s použitím .NET API.
documentationcenter: windows
author: dimazaid
manager: kpiteira
editor: spelluru
services: notification-hubs
ms.assetid: 5aef50f4-80b3-460e-a9a7-7435001273bd
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: 8d051107a5e114ed8aa5f4b5a629a439519157b3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs zabezpečené Push
> [!div class="op_single_selector"]
> * [Univerzální pro Windows](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>Přehled
Podpora nabízená oznámení v Microsoft Azure umožňuje přístup k infrastruktuře snadno použitelnou, multiplatformní a upraveným nabízené, což výrazně zjednodušuje implementaci nabízená oznámení spotřebních a podnikových aplikací pro mobilní platformy.

Kvůli zákonným omezení zabezpečení, někdy aplikace může chtít zahrnout něco v oznámení, kterou nelze přenést prostřednictvím infrastrukturu pro standardní nabízená oznámení. Tento kurz popisuje, jak zajistit stejné prostředí posíláním důvěrných informací o prostřednictvím zabezpečeného a ověřené připojení mezi klientské zařízení a back-end aplikace.

Na vysoké úrovni tok je následující:

1. Back-end aplikace:
   * Zabezpečení datové úložiště v databázi back-end.
   * ID tohoto oznámení se odešle do zařízení (zabezpečené nebudou odeslány žádné informace).
2. Aplikace na zařízení, když obdrží oznámení:
   * Zařízení kontaktuje back-end vyžaduje zabezpečené datové části.
   * Aplikace můžete zobrazit datové části jako upozornění na zařízení.

Je důležité si uvědomit, že v předchozím toku (a v tomto kurzu) předpokládáme, že zařízení ukládá ověřovací token do místního úložiště, po přihlášení uživatele. Zaručí se tím úplně jednoduché prostředí, protože zařízení můžete načíst pomocí tohoto tokenu zabezpečení datové na oznámení. Pokud vaše aplikace nejsou uložené tokeny ověřování v zařízení, nebo pokud tyto tokeny můžete vypršela platnost, by měla aplikace zařízení při přijetí oznámení zobrazit obecné oznámení uživateli zobrazuje výzvu spusťte aplikaci. Aplikace pak ověřuje uživatele a ukazuje datová část oznámení.

V tomto kurzu zabezpečení nabízené ukazuje, jak bezpečně odesílání nabízených oznámení. Tento kurz je založený na [upozornění uživatelů](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) kurzu, a proto kroky musí dokončit v tomto kurzu první.

> [!NOTE]
> V tomto kurzu se předpokládá, že jste vytvořili a nakonfigurovali vaše Centrum oznámení, jak je popsáno v [Začínáme s Notification Hubs (pro Windows Store)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
> Všimněte si také, že Windows Phone 8.1 vyžaduje pověření systému Windows (ne Windows Phone), a že úlohy na pozadí nefungují na Windows Phone 8.0 nebo Silverlight 8.1. Pro aplikace pro Windows Store, můžete přijímat oznámení prostřednictvím úlohy na pozadí pouze v případě, že aplikace je povoleno uzamčení obrazovky (klikněte na zaškrtávací políčko v Appmanifest).
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-windows-phone-project"></a>Upravit projektu Windows Phone
1. V **NotifyUserWindowsPhone** projekt, přidejte následující kód do souboru App.xaml.cs k registraci úlohy na pozadí push. Přidejte následující řádek kódu na konec metody `OnLaunched()`:
   
        RegisterBackgroundTask();
2. Stále v souboru App.xaml.cs přidejte následující kód bezprostředně po `OnLaunched()` metoda:
   
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
3. Přidejte následující `using` příkazy v horní části souboru App.xaml.cs:
   
        using Windows.Networking.PushNotifications;
        using Windows.ApplicationModel.Background;
4. Ve Visual Studiu zvolte v nabídce **Soubor** možnost **Uložit vše**.

## <a name="create-the-push-background-component"></a>Vytvořit komponentu nabízené pozadí
Dalším krokem je vytvoření komponentu nabízených pozadí.

1. V Průzkumníku řešení klikněte pravým tlačítkem na uzel na nejvyšší úrovni řešení (**řešení SecurePush** v tomto případě), pak klikněte na tlačítko **přidat**, pak klikněte na tlačítko **nový projekt**.
2. Rozbalte položku **aplikacích pro Store**, pak klikněte na tlačítko **aplikace Windows Phone**, pak klikněte na tlačítko **komponenty prostředí Windows Runtime (Windows Phone)**. Název projektu **PushBackgroundComponent**a potom klikněte na **OK** a vytvořte tak projekt.
   
    ![][12]
3. V Průzkumníku řešení klikněte pravým tlačítkem myši **PushBackgroundComponent (Windows Phone 8.1)** projektu a pak klikněte na **přidat**, pak klikněte na tlačítko **třída**. Pojmenujte novou třídu **PushBackgroundTask.cs**. Klikněte na tlačítko **přidat** ke generování třídy.
4. Nahradí celý obsah **PushBackgroundComponent** definici oboru názvů následujícím kódem, nahraďte zástupný symbol `{back-end endpoint}` s koncovým bodem back-end získali při nasazování back-end:
   
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
5. V Průzkumníku řešení klikněte pravým tlačítkem myši **PushBackgroundComponent (Windows Phone 8.1)** projektu a pak klikněte na **spravovat balíčky NuGet**.
6. Na levé straně klikněte na **Online**.
7. V **vyhledávání** zadejte **klienta Http**.
8. V seznamu výsledků klepněte na **knihovny klienta HTTP Microsoft**a potom klikněte na **nainstalovat**. Dokončete instalaci.
9. Zpět v NuGet **vyhledávání** zadejte **Json.net**. Nainstalujte **Json.NET** balíček a potom zavřete okno Správce balíčků NuGet.
10. Přidejte následující `using` příkazy v horní části **PushBackgroundTask.cs** souboru:
    
        using Windows.ApplicationModel.Background;
        using Windows.Networking.PushNotifications;
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using Windows.UI.Notifications;
        using Windows.Data.Xml.Dom;
11. V Průzkumníku řešení v **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu, klikněte pravým tlačítkem na **odkazy**, pak klikněte na tlačítko **přidat odkaz na...** . V dialogovém okně Správce odkazů, zaškrtněte políčko vedle **PushBackgroundComponent**a potom klikněte na **OK**.
12. V Průzkumníku řešení klikněte dvakrát na **Package.appxmanifest** v **NotifyUserWindowsPhone (Windows Phone 8.1)** projektu. V části **oznámení**, nastavte **informační podporující** k **Ano**.
    
    ![][3]
13. Pořád ještě v **Package.appxmanifest**, klikněte **deklarace** v horní nabídce. V **dostupné deklarace** rozevíracího seznamu, klikněte na tlačítko **úlohy na pozadí**a potom klikněte na **přidat**.
14. V **Package.appxmanifest**v části **vlastnosti**, zkontrolujte **nabízená oznámení**.
15. V **Package.appxmanifest**v části **nastavení aplikace**, typ **PushBackgroundComponent.PushBackgroundTask** v **vstupní bod** pole.
    
    ![][13]
16. V nabídce **Soubor** klikněte na **Uložit vše**.

## <a name="run-the-application"></a>Spuštění aplikace
Ke spuštění aplikace, postupujte takto:

1. V sadě Visual Studio, spusťte **AppBackend** aplikace webového rozhraní API. Zobrazí se webová stránka ASP.NET.
2. V sadě Visual Studio, spusťte **NotifyUserWindowsPhone (Windows Phone 8.1)** aplikace Windows Phone. Emulátor Windows Phone spustí a automaticky načte aplikaci.
3. V **NotifyUserWindowsPhone** aplikace uživatelského rozhraní, zadejte uživatelské jméno a heslo. Mohou to být libovolný řetězec, ale musí být stejnou hodnotu.
4. V **NotifyUserWindowsPhone** aplikace uživatelského rozhraní, klikněte na tlačítko **přihlášení a registrace**. Pak klikněte na tlačítko **odeslat nabízené**.

[3]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push3.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-secure-push/notification-hubs-secure-push13.png
