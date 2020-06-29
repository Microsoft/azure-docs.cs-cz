---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: fc479522b3fd436ff02ff6b8985bdeddb66da90a
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448733"
---
### <a name="create-the-xamarinforms-solution"></a>Vytvoření řešení Xamarin. Forms

1. V **aplikaci Visual Studio**vytvořte nové řešení **Xamarin. Forms** s použitím **prázdné aplikace Forms** jako šablony a zadáním *PushDemo* pro **název projektu**.

    > [!NOTE]
    > V dialogovém okně **Konfigurovat prázdné formuláře aplikace** zajistěte, aby se **identifikátor organizace** shodoval s hodnotou, kterou jste použili dříve a aby byly zaškrtnuté cíle pro **Android** i **iOS** .

1. **Ovládací prvek**  +  **Klikněte** na řešení *PushDemo* a pak zvolte **Aktualizovat balíčky NuGet**.
1. **Ovládací prvek**  +  **Klikněte** na řešení *PushDemo* a pak zvolte **Spravovat balíčky NuGet.**..
1. Vyhledejte **Newtonsoft.Js** a ujistěte se, že je zaškrtnutá.
1. Klikněte na **Přidat balíčky**a po zobrazení výzvy k přijetí licenčních podmínek klikněte na **přijmout** .
1. Sestavte a spusťte aplikaci na každé cílové platformě (**příkaz**  +  **ENTER**) pro otestování, že aplikace se šablonami spouští na vašich zařízeních.

### <a name="implement-the-cross-platform-components"></a>Implementace komponent pro různé platformy

1. **Ovládací prvek**  +  **Klikněte** na projekt **PushDemo** , v nabídce **Přidat** zvolte **Nová složka** a pak klikněte na **Přidat** pomocí *modelů* jako **název složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **modely** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **Obecné**  >  **prázdné třídy**, zadejte *DeviceInstallation.cs*a pak přidejte následující implementaci.

    ```csharp
    using System.Collections.Generic;
    using Newtonsoft.Json;

    namespace PushDemo.Models
    {
        public class DeviceInstallation
        {
            [JsonProperty("installationId")]
            public string InstallationId { get; set; }

            [JsonProperty("platform")]
            public string Platform { get; set; }

            [JsonProperty("pushChannel")]
            public string PushChannel { get; set; }

            [JsonProperty("tags")]
            public List<string> Tags { get; set; } = new List<string>();
        }
    }
    ```

1. Do složky **modely** s názvem *PushDemoAction.cs* přidejte **prázdný výčet** s následující implementací.

    ```csharp
    namespace PushDemo.Models
    {
        public enum PushDemoAction
        {
            ActionA,
            ActionB
        }
    }
    ```

1. Přidejte do projektu **PushDemo** novou složku s názvem *služby* a potom do této složky s názvem *ServiceContainer.cs* přidejte **prázdnou třídu** s následující implementací.

     ```csharp
    using System;
    using System.Collections.Generic;

    namespace PushDemo.Services
    {
        public static class ServiceContainer
        {
            static readonly Dictionary<Type, Lazy<object>> services
                = new Dictionary<Type, Lazy<object>>();

            public static void Register<T>(Func<T> function)
                => services[typeof(T)] = new Lazy<object>(() => function());

            public static T Resolve<T>()
                => (T)Resolve(typeof(T));

            public static object Resolve(Type type)
            {
                {
                    if (services.TryGetValue(type, out var service))
                        return service.Value;

                    throw new KeyNotFoundException($"Service not found for type '{type}'");
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Toto je zkrácená verze třídy [platný ServiceContainer](https://github.com/xamcat/mobcat-library/blob/master/MobCAT/ServiceContainer.cs) z úložiště [XamCAT](https://github.com/xamcat/mobcat-library) . Použije se jako IoC kontejner (inverze ovládacího prvku).

1. Přidejte **prázdné rozhraní** do složky **Services** s názvem *IDeviceInstallationService.cs*a poté přidejte následující kód.

    ```csharp
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IDeviceInstallationService
        {
            string Token { get; set; }
            bool NotificationsSupported { get; }
            string GetDeviceId();
            DeviceInstallation GetDeviceInstallation(params string[] tags);
        }
    }
    ```

    > [!NOTE]
    > Toto rozhraní se bude implementovat každým cílem později, aby se zajistila funkcionalita specifická pro platformu a **DeviceInstallation** informace, které vyžaduje služba back-end.

1. Do složky **Services** s názvem *INotificationRegistrationService.cs*přidejte další **prázdné rozhraní** a přidejte následující kód.  

    ```csharp
    using System.Threading.Tasks;

    namespace PushDemo.Services
    {
        public interface INotificationRegistrationService
        {
            Task DeregisterDeviceAsync();
            Task RegisterDeviceAsync(params string[] tags);
            Task RefreshRegistrationAsync();
        }
    }
    ```

    > [!NOTE]
    > Tím se zpracuje interakce mezi klientem a back-end službou.

1. Do složky **Services** s názvem *INotificationActionService.cs*přidejte další **prázdné rozhraní** a přidejte následující kód.  

    ```csharp
    namespace PushDemo.Services
    {
        public interface INotificationActionService
        {
            void TriggerAction(string action);
        }
    }
    ```

    > [!NOTE]
    > Slouží jako jednoduchý mechanismus pro centralizaci manipulace s akcemi oznámení.

1. Přidejte **prázdné rozhraní** do složky **Services** s názvem *IPushDemoNotificationActionService.cs* , která je odvozena z *INotificationActionService*, s následující implementací.  

    ```csharp
    using System;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public interface IPushDemoNotificationActionService : INotificationActionService
        {
            event EventHandler<PushDemoAction> ActionTriggered;
        }
    }
    ```

    > [!NOTE]
    > Tento typ je specifický pro aplikaci **PushDemo** a používá výčet **PushDemoAction** k identifikaci akce, která se aktivuje způsobem silného typu.

1. Přidejte **prázdnou třídu** do složky **Services** s názvem *NotificationRegistrationService.cs* implementující *INotificationRegistrationService* s následujícím kódem.

    ```csharp
    using System;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using PushDemo.Models;
    using Xamarin.Essentials;

    namespace PushDemo.Services
    {
        public class NotificationRegistrationService : INotificationRegistrationService
        {
            const string RequestUrl = "api/notifications/installations";
            const string CachedDeviceTokenKey = "cached_device_token";
            const string CachedTagsKey = "cached_tags";

            string _baseApiUrl;
            HttpClient _client;
            IDeviceInstallationService _deviceInstallationService;

            public NotificationRegistrationService(string baseApiUri, string apiKey)
            {
                _client = new HttpClient();
                _client.DefaultRequestHeaders.Add("Accept", "application/json");
                _client.DefaultRequestHeaders.Add("apikey", apiKey);

                _baseApiUrl = baseApiUri;
            }

            IDeviceInstallationService DeviceInstallationService
                => _deviceInstallationService ??
                    (_deviceInstallationService = ServiceContainer.Resolve<IDeviceInstallationService>());

            public async Task DeregisterDeviceAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                if (cachedToken == null)
                    return;

                var deviceId = DeviceInstallationService?.GetDeviceId();

                if (string.IsNullOrWhiteSpace(deviceId))
                    throw new Exception("Unable to resolve an ID for the device.");

                await SendAsync(HttpMethod.Delete, $"{RequestUrl}/{deviceId}")
                    .ConfigureAwait(false);

                SecureStorage.Remove(CachedDeviceTokenKey);
                SecureStorage.Remove(CachedTagsKey);
            }

            public async Task RegisterDeviceAsync(params string[] tags)
            {
                var deviceInstallation = DeviceInstallationService?.GetDeviceInstallation(tags);

                await SendAsync<DeviceInstallation>(HttpMethod.Put, RequestUrl, deviceInstallation)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedDeviceTokenKey, deviceInstallation.PushChannel)
                    .ConfigureAwait(false);

                await SecureStorage.SetAsync(CachedTagsKey, JsonConvert.SerializeObject(tags));
            }

            public async Task RefreshRegistrationAsync()
            {
                var cachedToken = await SecureStorage.GetAsync(CachedDeviceTokenKey)
                    .ConfigureAwait(false);

                var serializedTags = await SecureStorage.GetAsync(CachedTagsKey)
                    .ConfigureAwait(false);

                if (string.IsNullOrWhiteSpace(cachedToken) ||
                    string.IsNullOrWhiteSpace(serializedTags) ||
                    string.IsNullOrWhiteSpace(DeviceInstallationService.Token) ||
                    cachedToken == DeviceInstallationService.Token)
                    return;

                var tags = JsonConvert.DeserializeObject<string[]>(serializedTags);

                await RegisterDeviceAsync(tags);
            }

            async Task SendAsync<T>(HttpMethod requestType, string requestUri, T obj)
            {
                string serializedContent = null;

                await Task.Run(() => serializedContent = JsonConvert.SerializeObject(obj))
                    .ConfigureAwait(false);

                await SendAsync(requestType, requestUri, serializedContent);
            }

            async Task SendAsync(
                HttpMethod requestType,
                string requestUri,
                string jsonRequest = null)
            {
                var request = new HttpRequestMessage(requestType, new Uri($"{_baseApiUrl}{requestUri}"));

                if (jsonRequest != null)
                    request.Content = new StringContent(jsonRequest, Encoding.UTF8, "application/json");

                var response = await _client.SendAsync(request).ConfigureAwait(false);

                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

    > [!NOTE]
    > Argument **apiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .

1. Přidejte **prázdnou třídu** do složky **Services** s názvem *PushDemoNotificationActionService.cs* implementující *IPushDemoNotificationActionService* s následujícím kódem.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using PushDemo.Models;

    namespace PushDemo.Services
    {
        public class PushDemoNotificationActionService : IPushDemoNotificationActionService
        {
            readonly Dictionary<string, PushDemoAction> _actionMappings = new Dictionary<string, PushDemoAction>
            {
                { "action_a", PushDemoAction.ActionA },
                { "action_b", PushDemoAction.ActionB }
            };

            public event EventHandler<PushDemoAction> ActionTriggered = delegate { };

            public void TriggerAction(string action)
            {
                if (!_actionMappings.TryGetValue(action, out var pushDemoAction))
                    return;

                List<Exception> exceptions = new List<Exception>();

                foreach (var handler in ActionTriggered?.GetInvocationList())
                {
                    try
                    {
                        handler.DynamicInvoke(this, pushDemoAction);
                    }
                    catch (Exception ex)
                    {
                        exceptions.Add(ex);
                    }
                }

                if (exceptions.Any())
                    throw new AggregateException(exceptions);
            }
        }
    }
    ```

1. Přidejte **prázdnou třídu** do projektu **PushDemo** s názvem *config.cs* s následující implementací.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            public static string ApiKey = "API_KEY";
            public static string BackendServiceEndpoint = "BACKEND_SERVICE_ENDPOINT";
        }
    }
    ```

    > [!NOTE]
    > Tato možnost se používá jako jednoduchý způsob, jak zachovat tajné kódy ze správy zdrojového kódu. Tyto hodnoty můžete nahradit jako součást automatizovaného sestavení nebo je přepsat pomocí místní částečné třídy. Provedete to v dalším kroku.
    >
    > Pole **ApiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .

1. Přidejte do projektu **PushDemo** další **prázdnou třídu** . Tato doba se nazývá *config. local_secrets. cs* s následující implementací.  

    ```csharp
    namespace PushDemo
    {
        public static partial class Config
        {
            static Config()
            {
                ApiKey = "<your_api_key>";
                BackendServiceEndpoint = "<your_api_app_url>";
            }
        }
    }
    ```

    > [!NOTE]
    > Zástupné hodnoty nahraďte vlastními. Měli byste si je poznamenat, když jste vytvořili back-end službu. Adresa URL **aplikace API** by měla být ``https://<api_app_name>.azurewebsites.net/`` . Nezapomeňte přidat ``*.local_secrets.*`` do souboru gitignore, abyste se vyhnuli potvrzení tohoto souboru.
    >
    > Pole **ApiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .

1. Přidejte **prázdnou třídu** do projektu **PushDemo** s názvem *bootstrap.cs* s následující implementací.  

    ```csharp
    using System;
    using PushDemo.Services;

    namespace PushDemo
    {
        public static class Bootstrap
        {
            public static void Begin(Func<IDeviceInstallationService> deviceInstallationService)
            {
                ServiceContainer.Register(deviceInstallationService);

                ServiceContainer.Register<IPushDemoNotificationActionService>(()
                    => new PushDemoNotificationActionService());

                ServiceContainer.Register<INotificationRegistrationService>(()
                    => new NotificationRegistrationService(
                        Config.BackendServiceEndpoint,
                        Config.ApiKey));
            }
        }
    }
    ```

    > [!NOTE]
    > Metoda **Begin** bude volána každou platformou, když aplikace začne přecházet do implementace **IDeviceInstallationService**specifické pro konkrétní platformu.
    >
    > Argument konstruktoru **NotificationRegistrationService** **apiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) .

### <a name="implement-the-cross-platform-ui"></a>Implementace uživatelského rozhraní pro různé platformy

1. V projektu **PushDemo** otevřete **MainPage. XAML** a nahraďte ovládací prvek **StackLayout** následujícím.

    ```xml
    <StackLayout VerticalOptions="EndAndExpand"  
                 HorizontalOptions="FillAndExpand"
                 Padding="20,40">
        <Button x:Name="RegisterButton"
                Text="Register"
                Clicked="RegisterButtonClicked" />
        <Button x:Name="DeregisterButton"
                Text="Deregister"
                Clicked="DeregisterButtonClicked" />
    </StackLayout>
    ```

1. Nyní v **MainPage.XAML.cs**přidejte pole, které je **jen pro čtení** , a uložte odkaz na implementaci **INotificationRegistrationService** .

    ```csharp
    readonly INotificationRegistrationService _notificationRegistrationService;
    ```

1. V konstruktoru **MainPage** vyřešte implementaci **INotificationRegistrationService** pomocí **platný ServiceContainer** a přiřaďte ji do pole pro zálohování *_notificationRegistrationService_* .

    ```csharp
    public MainPage()
    {
        InitializeComponent();

        _notificationRegistrationService =
            ServiceContainer.Resolve<INotificationRegistrationService>();
    }
    ```

1. Implementujte obslužné rutiny událostí pro tlačítka **RegisterButton** a **DeregisterButton** **kliknutím** na události, které volají příslušné metody **registrace registru** / **Deregister** .

    ```csharp
    void RegisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.RegisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device registered"); });

    void DeregisterButtonClicked(object sender, EventArgs e)
        => _notificationRegistrationService.DeregisterDeviceAsync().ContinueWith((task)
            => { ShowAlert(task.IsFaulted ?
                    task.Exception.Message :
                    $"Device deregistered"); });

    void ShowAlert(string message)
        => MainThread.BeginInvokeOnMainThread(()
            => DisplayAlert("PushDemo", message, "OK").ContinueWith((task)
                => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. Nyní v **App.XAML.cs**ověřte, že jsou odkazovány následující obory názvů.

    ```csharp
    using PushDemo.Models;
    using PushDemo.Services;
    using Xamarin.Essentials;
    using Xamarin.Forms;
    ```

1. Implementujte obslužnou rutinu události pro událost **IPushDemoNotificationActionService** **ActionTriggered** .

    ```csharp
    void NotificationActionTriggered(object sender, PushDemoAction e)
        => ShowActionAlert(e);

    void ShowActionAlert(PushDemoAction action)
        => MainThread.BeginInvokeOnMainThread(()
            => MainPage?.DisplayAlert("PushDemo", $"{action} action received", "OK")
                .ContinueWith((task) => { if (task.IsFaulted) throw task.Exception; }));
    ```

1. V konstruktoru **aplikace** vyřešte implementaci **IPushNotificationActionService** pomocí **platný ServiceContainer** a přihlaste se k odběru události **IPushDemoNotificationActionService** **ActionTriggered** .

    ```csharp
    public App()
    {
        InitializeComponent();

        ServiceContainer.Resolve<IPushDemoNotificationActionService>()
            .ActionTriggered += NotificationActionTriggered;

        MainPage = new MainPage();
    }
    ```

    > [!NOTE]
    > To je jednoduché k předvedení příjmu a šíření akcí nabízených oznámení. Obvykle by se tyto chyby zpracovaly v tichém režimu, například přejít k určitému zobrazení nebo aktualizovat některá data namísto zobrazení výstrahy prostřednictvím kořenové **stránky**, v tomto případě **MainPage** .
