---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: f0e83548d3ba3b353b471e2e3429a23421aec7b2
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081373"
---
### <a name="create-a-web-project"></a>Vytvoření webového projektu

1. V **aplikaci Visual Studio**vyberte **soubor**  >  **nové řešení**.

1. Jako další vyberte aplikaci **.NET Core**  >  **App**  >  **ASP.NET Core**  >  **API**  >  **Next**.
  
1. V dialogovém okně **Konfigurace nového ASP.NET Core webového rozhraní API** vyberte **cílovou architekturu** **.NET Core 3,1**.

1. Jako **název projektu** zadejte *PushDemoApi* a pak vyberte **vytvořit**.

1. Spusťte ladění (**příkaz**  +  **ENTER**) pro otestování aplikace v šabloně.

    > [!NOTE]
    > Aplikace v šabloně je nakonfigurovaná tak, aby používala **WeatherForecastController** jako *launchUrl*. Tato vlastnost je nastavena ve **vlastnostech**  >  **launchSettings.jszapnuta**.  
    >
    > Pokud se zobrazí výzva s **neplatným certifikátem vývoje** , který se našel:
    >
    > 1. Kliknutím na **Ano** souhlasíte s tím, že spustíte nástroj dotnet dev-certs https a opravíte ho. Nástroj dotnet dev-certs HTTPS pak vyzve k zadání hesla pro certifikát a heslo pro řetězec klíčů.
    >
    > 1. Po zobrazení výzvy k **instalaci a důvěřování novému certifikátu**klikněte na **Ano** a pak zadejte heslo pro řetězec klíčů.

1. Rozbalte složku **Controllers** a pak odstraňte **WeatherForecastController.cs**.

1. Odstraňte **WeatherForecast.cs**.

1. **Ovládací prvek**  +  **Klikněte** na projekt **PushDemoApi** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Pomocí [nástroje Správce tajných klíčů](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=linux#secret-manager)nastavte hodnoty místních konfigurací. Odpojuje tajné klíče z řešení zajišťuje, že se neukončí ve správě zdrojového kódu. Otevřete **terminál** a přejděte do adresáře souboru projektu a spusťte následující příkazy:

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set "NotificationHub:Name" <value>
    dotnet user-secrets set "NotificationHub:ConnectionString" <value>
    ```

    Zástupné hodnoty nahraďte vlastním názvem centra oznámení a hodnotami připojovacích řetězců. V části [vytvoření centra oznámení](#create-a-notification-hub) jste si ho poznamenali. V opačném případě je můžete vyhledat v [Azure](https://portal.azure.com).

    **NotificationsHub: název**:  
    Podívejte se na *název* v souhrnu **základních** informací v horní části **přehledu**.  

    **NotificationHub: ConnectionString**:  
    Zobrazit *DefaultFullSharedAccessSignature* v **zásadách přístupu**

    > [!NOTE]
    > V produkčních scénářích se můžete podívat na možnosti, jako je [Azure webtrezor](https://azure.microsoft.com/services/key-vault) , pro bezpečné uložení připojovacího řetězce. V zájmu jednoduchosti budou tajná klíčová slova přidána do nastavení aplikace [Azure App Service](https://azure.microsoft.com/services/app-service/) .

### <a name="authenticate-clients-using-an-api-key-optional"></a>Ověřování klientů pomocí klíče rozhraní API (volitelné)

Klíče rozhraní API nejsou stejně bezpečné jako tokeny, ale budou pro účely tohoto kurzu dostačující. Klíč rozhraní API se dá snadno nakonfigurovat prostřednictvím [middlewaru ASP.NET](https://docs.microsoft.com/aspnet/core/fundamentals/middleware/?view=aspnetcore-3.1).

1. Přidejte **klíč rozhraní API** do hodnot místních konfigurací.

    ```bash
    dotnet user-secrets set "Authentication:ApiKey" <value>
    ```

    > [!NOTE]
    > Zástupnou hodnotu byste měli nahradit vlastními a poznamenat si ji.

1. **Ovládací prvek**  +  **Klikněte** na projekt **PushDemoApi** , v nabídce **Přidat** zvolte **Nová složka** a pak klikněte na **Přidat** pomocí *ověřování* jako **název složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **ověřování** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **Obecné**  >  **prázdné třídy**, jako **název**zadejte *ApiKeyAuthOptions.cs* a pak klikněte na **Nový** přidání následující implementace.

    ```csharp
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthOptions : AuthenticationSchemeOptions
        {
            public const string DefaultScheme = "ApiKey";
            public string Scheme => DefaultScheme;
            public string ApiKey { get; set; }
        }
    }
    ```

1. Do složky pro **ověřování** s názvem *ApiKeyAuthHandler.cs*přidejte další **prázdnou třídu** a pak přidejte následující implementaci.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Security.Claims;
    using System.Text.Encodings.Web;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;

    namespace PushDemoApi.Authentication
    {
        public class ApiKeyAuthHandler : AuthenticationHandler<ApiKeyAuthOptions>
        {
            const string ApiKeyIdentifier = "apikey";

            public ApiKeyAuthHandler(
                IOptionsMonitor<ApiKeyAuthOptions> options,
                ILoggerFactory logger,
                UrlEncoder encoder,
                ISystemClock clock)
                : base(options, logger, encoder, clock) {}

            protected override Task<AuthenticateResult> HandleAuthenticateAsync()
            {
                string key = string.Empty;

                if (Request.Headers[ApiKeyIdentifier].Any())
                {
                    key = Request.Headers[ApiKeyIdentifier].FirstOrDefault();
                }
                else if (Request.Query.ContainsKey(ApiKeyIdentifier))
                {
                    if (Request.Query.TryGetValue(ApiKeyIdentifier, out var queryKey))
                        key = queryKey;
                }

                if (string.IsNullOrWhiteSpace(key))
                    return Task.FromResult(AuthenticateResult.Fail("No api key provided"));

                if (!string.Equals(key, Options.ApiKey, StringComparison.Ordinal))
                    return Task.FromResult(AuthenticateResult.Fail("Invalid api key."));

                var identities = new List<ClaimsIdentity> {
                    new ClaimsIdentity("ApiKeyIdentity")
                };

                var ticket = new AuthenticationTicket(
                    new ClaimsPrincipal(identities), Options.Scheme);

                return Task.FromResult(AuthenticateResult.Success(ticket));
            }
        }
    }
    ```

    > [!NOTE]
    > [Obslužná rutina ověřování](https://docs.microsoft.com/aspnet/core/security/authentication/?view=aspnetcore-3.1#authentication-handler) je typ, který implementuje chování schématu, v tomto případě vlastního klíčového schématu rozhraní API.

1. Do složky pro **ověřování** s názvem *ApiKeyAuthenticationBuilderExtensions.cs*přidejte další **prázdnou třídu** a pak přidejte následující implementaci.

    ```csharp
    using System;
    using Microsoft.AspNetCore.Authentication;

    namespace PushDemoApi.Authentication
    {
        public static class AuthenticationBuilderExtensions
        {
            public static AuthenticationBuilder AddApiKeyAuth(
                this AuthenticationBuilder builder,
                Action<ApiKeyAuthOptions> configureOptions)
            {
                return builder
                    .AddScheme<ApiKeyAuthOptions, ApiKeyAuthHandler>(
                        ApiKeyAuthOptions.DefaultScheme,
                        configureOptions);
            }
        }
    }
    ```

    > [!NOTE]
    > Tato rozšiřující metoda zjednodušuje konfigurační kód middlewaru v **Startup.cs** , který usnadňuje čtení a obecně je jednodušší.

1. V **Startup.cs**aktualizujte metodu **ConfigureServices** a nakonfigurujte ověřování klíče rozhraní API pod voláním **služeb. Metoda AddControllers**

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllers();

        services.AddAuthentication(options =>
        {
            options.DefaultAuthenticateScheme = ApiKeyAuthOptions.DefaultScheme;
            options.DefaultChallengeScheme = ApiKeyAuthOptions.DefaultScheme;
        }).AddApiKeyAuth(Configuration.GetSection("Authentication").Bind);
    }
    ```

1. Pořád v **Startup.cs**aktualizujte metodu **Configure** tak, aby na **IApplicationBuilder**aplikace volala metody rozšíření **UseAuthentication** a **UseAuthorization** . Ujistěte se, že tyto metody jsou volány po **UseRouting** a před **aplikací. UseEndpoints**.

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseHttpsRedirection();

        app.UseRouting();

        app.UseAuthentication();

        app.UseAuthorization();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllers();
        });
    }
    ```

    > [!NOTE]
    > Volání **UseAuthentication** registruje middleware, který používá dříve registrovaná schémata ověřování (z **ConfigureServices**). Tato metoda musí být volána před jakýmkoli middlewarem, který závisí na ověřených uživatelích.

### <a name="add-dependencies-and-configure-services"></a>Přidat závislosti a nakonfigurovat služby

ASP.NET Core podporuje vzor návrhu softwaru pro [vkládání závislostí (di)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-3.1) , což je technika pro dosažení [inverze ovládacího prvku (IOC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mezi třídami a jejich závislostmi.  

Použití centra oznámení a [Notification HUBS SDK pro back-endové operace](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) je zapouzdřeno v rámci služby. Služba je registrována a zpřístupněna prostřednictvím vhodné abstrakce.

1. **Ovládací prvek**  +  **Klikněte** na složku **závislosti** a pak zvolte **Spravovat balíčky NuGet...**.

1. Vyhledejte **Microsoft. Azure. NotificationHubs** a ujistěte se, že je zaškrtnuté.

1. Klikněte na **Přidat balíčky**a po zobrazení výzvy k přijetí licenčních podmínek klikněte na **přijmout** .

1. **Ovládací prvek**  +  **Klikněte** na projekt **PushDemoApi** , v nabídce **Přidat** zvolte **Nová složka** a pak klikněte na **Přidat** pomocí *modelů* jako **název složky**.

1. **Ovládací prvek**  +  **Klikněte** na složku **modely** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **Obecné**  >  **prázdné třídy**, jako **název**zadejte *PushTemplates.cs* a pak klikněte na **Nový** přidání následující implementace.

    ```csharp
    namespace PushDemoApi.Models
    {
        public class PushTemplates
        {
            public class Generic
            {
                public const string Android = "{ \"notification\": { \"title\" : \"PushDemo\", \"body\" : \"$(alertMessage)\"}, \"data\" : { \"action\" : \"$(alertAction)\" } }";
                public const string iOS = "{ \"aps\" : {\"alert\" : \"$(alertMessage)\"}, \"action\" : \"$(alertAction)\" }";
            }

            public class Silent
            {
                public const string Android = "{ \"data\" : {\"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\"} }";
                public const string iOS = "{ \"aps\" : {\"content-available\" : 1, \"apns-priority\": 5, \"sound\" : \"\", \"badge\" : 0}, \"message\" : \"$(alertMessage)\", \"action\" : \"$(alertAction)\" }";
            }
        }
    }
    ```

    > [!NOTE]
    > Tato třída obsahuje datovou část oznámení s tokeny pro obecná a tichá oznámení požadovaná v tomto scénáři. Datové části jsou definovány mimo [instalaci](https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.installation?view=azure-dotnet) , což umožňuje experimentování bez nutnosti aktualizovat existující instalace prostřednictvím služby. Zpracování změn v instalacích tímto způsobem je mimo rozsah tohoto kurzu. V produkčním prostředí zvažte [vlastní šablony](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-templates-cross-platform-push-messages).

1. Vyberte **Obecné**  >  **prázdné třídy**, jako **název**zadejte *DeviceInstallation.cs* a pak klikněte na **Nový** přidání následující implementace.

    ```csharp
    using System.Collections.Generic;
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class DeviceInstallation
        {
            [Required]
            public string InstallationId { get; set; }

            [Required]
            public string Platform { get; set; }

            [Required]
            public string PushChannel { get; set; }

            public IList<string> Tags { get; set; } = Array.Empty<string>();
        }
    }
    ```

1. Do složky **modely** s názvem *NotificationRequest.cs*přidejte další **prázdnou třídu** a pak přidejte následující implementaci.

    ```csharp
    using System;

    namespace PushDemoApi.Models
    {
        public class NotificationRequest
        {
            public string Text { get; set; }
            public string Action { get; set; }
            public string[] Tags { get; set; } = Array.Empty<string>();
            public bool Silent { get; set; }
        }
    }
    ```

1. Do složky **modely** s názvem *NotificationHubOptions.cs*přidejte další **prázdnou třídu** a pak přidejte následující implementaci.

    ```csharp
    using System.ComponentModel.DataAnnotations;

    namespace PushDemoApi.Models
    {
        public class NotificationHubOptions
        {
            [Required]
            public string Name { get; set; }

            [Required]
            public string ConnectionString { get; set; }
        }
    }
    ```

1. Přidejte novou složku do projektu **PushDemoApi** s názvem *Services*.

1. Přidejte **prázdné rozhraní** do složky **Services** s názvem *INotificationService.cs*a pak přidejte následující implementaci.

    ```csharp
    using System.Threading.Tasks;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public interface INotificationService
        {
            Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token);
            Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token);
            Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token);
        }
    }
    ```

1. Přidejte **prázdnou třídu** do složky **Services** s názvem *NotificationHubsService.cs*a poté přidejte následující kód, který implementuje rozhraní **INotificationService** :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Options;
    using PushDemoApi.Models;

    namespace PushDemoApi.Services
    {
        public class NotificationHubService : INotificationService
        {
            readonly NotificationHubClient _hub;
            readonly Dictionary<string, NotificationPlatform> _installationPlatform;
            readonly ILogger<NotificationHubService> _logger;

            public NotificationHubService(IOptions<NotificationHubOptions> options, ILogger<NotificationHubService> logger)
            {
                _logger = logger;
                _hub = NotificationHubClient.CreateClientFromConnectionString(
                    options.Value.ConnectionString,
                    options.Value.Name);

                _installationPlatform = new Dictionary<string, NotificationPlatform>
                {
                    { nameof(NotificationPlatform.Apns).ToLower(), NotificationPlatform.Apns },
                    { nameof(NotificationPlatform.Fcm).ToLower(), NotificationPlatform.Fcm }
                };
            }

            public async Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(deviceInstallation?.InstallationId) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.Platform) ||
                    string.IsNullOrWhiteSpace(deviceInstallation?.PushChannel))
                    return false;

                var installation = new Installation()
                {
                    InstallationId = deviceInstallation.InstallationId,
                    PushChannel = deviceInstallation.PushChannel,
                    Tags = deviceInstallation.Tags
                };

                if (_installationPlatform.TryGetValue(deviceInstallation.Platform, out var platform))
                    installation.Platform = platform;
                else
                    return false;

                try
                {
                    await _hub.CreateOrUpdateInstallationAsync(installation, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken token)
            {
                if (string.IsNullOrWhiteSpace(installationId))
                    return false;

                try
                {
                    await _hub.DeleteInstallationAsync(installationId, token);
                }
                catch
                {
                    return false;
                }

                return true;
            }

            public async Task<bool> RequestNotificationAsync(NotificationRequest notificationRequest, CancellationToken token)
            {
                if ((notificationRequest.Silent &&
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
                    (!notificationRequest.Silent &&
                    (string.IsNullOrWhiteSpace(notificationRequest?.Text)) ||
                    string.IsNullOrWhiteSpace(notificationRequest?.Action)))
                    return false;

                var androidPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.Android :
                    PushTemplates.Generic.Android;

                var iOSPushTemplate = notificationRequest.Silent ?
                    PushTemplates.Silent.iOS :
                    PushTemplates.Generic.iOS;

                var androidPayload = PrepareNotificationPayload(
                    androidPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                var iOSPayload = PrepareNotificationPayload(
                    iOSPushTemplate,
                    notificationRequest.Text,
                    notificationRequest.Action);

                try
                {
                    if (notificationRequest.Tags.Length == 0)
                    {
                        // This will broadcast to all users registered in the notification hub
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, token);
                    }
                    else if (notificationRequest.Tags.Length <= 20)
                    {
                        await SendPlatformNotificationsAsync(androidPayload, iOSPayload, notificationRequest.Tags, token);
                    }
                    else
                    {
                        var notificationTasks = notificationRequest.Tags
                            .Select((value, index) => (value, index))
                            .GroupBy(g => g.index / 20, i => i.value)
                            .Select(tags => SendPlatformNotificationsAsync(androidPayload, iOSPayload, tags, token));

                        await Task.WhenAll(notificationTasks);
                    }

                    return true;
                }
                catch (Exception e)
                {
                    _logger.LogError(e, "Unexpected error sending notification");
                    return false;
                }
            }

            string PrepareNotificationPayload(string template, string text, string action) => template
                .Replace("$(alertMessage)", text, StringComparison.InvariantCulture)
                .Replace("$(alertAction)", action, StringComparison.InvariantCulture);

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, token)
                };

                return Task.WhenAll(sendTasks);
            }

            Task SendPlatformNotificationsAsync(string androidPayload, string iOSPayload, IEnumerable<string> tags, CancellationToken token)
            {
                var sendTasks = new Task[]
                {
                    _hub.SendFcmNativeNotificationAsync(androidPayload, tags, token),
                    _hub.SendAppleNativeNotificationAsync(iOSPayload, tags, token)
                };

                return Task.WhenAll(sendTasks);
            }
        }
    }
    ```

    > [!NOTE]
    > Výraz značky, který je k **SendTemplateNotificationAsync** k dispozici, je omezený na 20 značek. Pro většinu operátorů je omezen na 6, ale výraz v tomto případě obsahuje pouze ORs (| |). Pokud je v požadavku více než 20 značek, musí být rozděleny na více požadavků. Další podrobnosti najdete v dokumentaci k [výrazům směrování a značek](https://msdn.microsoft.com/library/azure/Dn530749.aspx?f=255&MSPPError=-2147217396) .

1. V **Startup.cs**aktualizujte metodu **ConfigureServices** pro přidání **NotificationHubsService** jako implementace typu Singleton **INotificationService**.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ...

        services.AddSingleton<INotificationService, NotificationHubService>();

        services.AddOptions<NotificationHubOptions>()
            .Configure(Configuration.GetSection("NotificationHub").Bind)
            .ValidateDataAnnotations();
    }
    ```

### <a name="create-the-notifications-api"></a>Vytvoření rozhraní API pro oznamování

1. **Ovládací prvek**  +  **Klikněte** na složku **řadiče** a pak zvolte **nový soubor...** v nabídce **Přidat** .

1. Vyberte **ASP.NET Core**  >  **Třída kontroleru webového rozhraní API**, jako **název**zadejte *NotificationsController* a pak klikněte na **Nový**.

1. Do horní části souboru přidejte následující obory názvů.

    ```csharp
    using System.ComponentModel.DataAnnotations;
    using System.Net;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    using Microsoft.AspNetCore.Mvc;
    using PushDemoApi.Models;
    using PushDemoApi.Services;
    ```

1. Aktualizujte kontroler šablon tak, aby byl odvozen z **ControllerBase** a byl upraven pomocí atributu **ApiController** .

    ```cs
    [ApiController]
    [Route("api/[controller]")]
    public class NotificationsController : ControllerBase
    {
        // Templated methods here
    }
    ```

    > [!NOTE]
    > Základní třída **kontroleru** poskytuje podporu pro zobrazení, ale v tomto případě to není nutné, takže **ControllerBase** lze použít místo toho.

1. Pokud se rozhodnete dokončit [ověřování klientů pomocí klíče rozhraní API](#authenticate-clients-using-an-api-key-optional) , měli byste **NotificationsController** vyplnit také atributem **autorizovat** .

    ```cs
    [Authorize]
    ```

1. Aktualizujte konstruktor tak, aby přijímal registrovanou instanci **INotificationService** jako argument a přiřadí ho ke členu jen pro čtení.

    ```cs
    readonly INotificationService _notificationService;

    public NotificationsController(INotificationService notificationService)
    {
        _notificationService = notificationService;
    }
    ```

1. V **launchSettings.jszapnuto** (ve složce **Properties** ) změňte **launchUrl** z `weatherforecast` na *rozhraní API/oznámení* tak, aby odpovídalo adrese URL zadané v atributu **Route** **RegistrationsController** .

1. Spusťte ladění (**příkaz**  +  **ENTER**), abyste ověřili, že aplikace pracuje s novým **NotificationsController** a vrací **neautorizovaný stav 401** .

    > [!NOTE]
    > Visual Studio nemusí automaticky spouštět aplikaci v prohlížeči. K otestování rozhraní API od tohoto okamžiku budete používat [metodu post](https://www.postman.com/downloads) .

1. Na nové kartě pro **[odeslání](https://www.postman.com/downloads)** zadejte požadavek, který chcete **získat** , a zadejte adresu níže.

    ```bash
    https://localhost:5001/api/notifications
    ```

    > [!NOTE]
    > Adresa localhost by měla odpovídat hodnotě **ApplicationUrl nebyla** , která byla nalezena ve **vlastnostech**  >  **launchSettings.js**. Výchozí hodnota by se `https://localhost:5001;http://localhost:5000` ale měla ověřit, jestli obdržíte odpověď 404.

1. Pokud se rozhodnete dokončit [ověřování klientů pomocí klíče rozhraní API](#authenticate-clients-using-an-api-key-optional) , nezapomeňte nakonfigurovat hlavičky požadavků tak, aby zahrnovaly hodnotu **apikey** .

   | Klíč                            | Hodnota                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Klikněte na tlačítko **Odeslat** .

    > [!NOTE]
    > Měli byste obdržet stav **200 OK** s nějakým obsahem **JSON** .
    >
    > Pokud obdržíte upozornění na **ověření certifikátu SSL** , můžete v **Nastavení**přepnout nastavení **[Odeslat](https://www.postman.com/downloads)** žádost o ověření certifikátu SSL.

1. Nahraďte metody třídy Template pomocí následujícího kódu.

    ```csharp
    [HttpPut]
    [Route("installations")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> UpdateInstallation(
        [Required]DeviceInstallation deviceInstallation)
    {
        var success = await _notificationService
            .CreateOrUpdateInstallationAsync(deviceInstallation, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpDelete()]
    [Route("installations/{installationId}")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<ActionResult> DeleteInstallation(
        [Required][FromRoute]string installationId)
    {
        var success = await _notificationService
            .DeleteInstallationByIdAsync(installationId, CancellationToken.None);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }

    [HttpPost]
    [Route("requests")]
    [ProducesResponseType((int)HttpStatusCode.OK)]
    [ProducesResponseType((int)HttpStatusCode.BadRequest)]
    [ProducesResponseType((int)HttpStatusCode.UnprocessableEntity)]
    public async Task<IActionResult> RequestPush(
        [Required]NotificationRequest notificationRequest)
    {
        if ((notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Action)) ||
            (!notificationRequest.Silent &&
            string.IsNullOrWhiteSpace(notificationRequest?.Text)))
            return new BadRequestResult();

        var success = await _notificationService
            .RequestNotificationAsync(notificationRequest, HttpContext.RequestAborted);

        if (!success)
            return new UnprocessableEntityResult();

        return new OkResult();
    }
    ```

### <a name="create-the-api-app"></a>Vytvoření aplikace API

Nyní vytvoříte [aplikaci API](https://azure.microsoft.com/services/app-service/api/) v [Azure App Service](https://docs.microsoft.com/azure/app-service/) pro hostování back-end služby.  

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Klikněte na **vytvořit prostředek**, vyhledejte a vyberte **aplikace API**a pak klikněte na **vytvořit**.

1. Aktualizujte následující pole a pak klikněte na **vytvořit**.

    **Název aplikace:**  
    Zadejte globálně jedinečný název **aplikace API** .

    **Formě**  
    Vyberte stejné cílové **předplatné** , ve kterém jste vytvořili centrum oznámení.

    **Skupina prostředků:**  
    Vyberte stejnou **skupinu prostředků** , ve které jste centrum oznámení vytvořili.

    **App Service plán/umístění:**  
    Vytvořit nový **plán App Service**  

    > [!NOTE]
    > Změňte výchozí možnost na plán, který zahrnuje podporu **SSL** . Jinak budete muset při práci s mobilní aplikací provést příslušné kroky, aby se zabránilo zablokování požadavků **http** .

    **Application Insights:**  
    Ponechte navrhovanou možnost (nový prostředek se vytvoří pomocí tohoto názvu) nebo vyberte existující prostředek.

1. Jakmile se **aplikace API** zřídí, přejděte k tomuto prostředku.

1. Poznamenejte si vlastnost **URL** v souhrnu **základních** informací v horní části **přehledu**. Tato adresa URL je *koncový bod back-endu* , který se použije později v tomto kurzu.

    > [!NOTE]
    > Adresa URL používá název aplikace API, který jste zadali dříve, ve formátu `https://<app_name>.azurewebsites.net` .

1. V seznamu vyberte **Konfigurace** (v části **Nastavení**).  

1. Pro každé z následujících nastavení klikněte na **nové nastavení aplikace** a zadejte **název** a **hodnotu**a pak klikněte na **OK**.

   | Name                               | Hodnota                          |
   | ---------------------------------- | ------------------------------ |
   | `Authentication:ApiKey`            | <api_key_value>                |
   | `NotificationHub:Name`             | <hub_name_value>               |
   | `NotificationHub:ConnectionString` | <hub_connection_string_value>  |

   > [!NOTE]
   > Jedná se o stejná nastavení, která jste definovali dříve v uživatelském nastavení. Měli byste být schopni tyto možnosti zkopírovat. Nastavení **ověřování: ApiKey** se vyžaduje jenom v případě, že se rozhodnete dokončit [ověřování klientů v části klíč rozhraní API](#authenticate-clients-using-an-api-key-optional) . V produkčních scénářích se můžete podívat na možnosti, jako je [Azure webtrezor](https://azure.microsoft.com/services/key-vault). V tomto případě byly přidány jako nastavení aplikace pro zjednodušení.

1. Po přidání všech nastavení aplikace klikněte na **Uložit**a pak **pokračovat**.

### <a name="publish-the-backend-service"></a>Publikování back-end služby

V dalším kroku nasadíte aplikaci do aplikace API, abyste ji mohli zpřístupnit ze všech zařízení.  

1. Pokud jste to ještě neudělali, změňte konfiguraci z **ladění** na **verzi** .

1. **Ovládací prvek**  +  **Klikněte** na **projekt PushDemoApi** a pak zvolte **publikovat do Azure...** v nabídce **publikovat** .

1. Pokud se zobrazí výzva, postupujte podle toku ověřování. Použijte účet, který jste použili v předchozím oddílu [Vytvoření aplikace API](#create-the-api-app) .

1. Vyberte **aplikaci API Azure App Service** , kterou jste předtím vytvořili ze seznamu jako cíl publikování, a pak klikněte na **publikovat**.

Po dokončení průvodce publikuje aplikaci do Azure a pak otevře aplikaci. Poznamenejte si **adresu URL** , pokud jste to ještě neudělali. Tato adresa URL je *koncový bod back-endu* , který se používá později v tomto kurzu.

### <a name="validating-the-published-api"></a>Ověřování publikovaného rozhraní API

1. V poli **[po](https://www.postman.com/downloads)** otevření nové karty nastavte požadavek na **post** a zadejte níže uvedenou adresu. Zástupný text nahraďte základní adresou, kterou jste si poznamenali v předchozím oddílu [publikování back-end služby](#publish-the-backend-service) .

    ```csharp
    https://<app_name>.azurewebsites.net/api/notifications/installations
    ```

    > [!NOTE]
    > Základní adresa by měla být ve formátu.``https://<app_name>.azurewebsites.net/``

1. Pokud se rozhodnete dokončit [ověřování klientů pomocí klíče rozhraní API](#authenticate-clients-using-an-api-key-optional) , nezapomeňte nakonfigurovat hlavičky požadavků tak, aby zahrnovaly hodnotu **apikey** .

   | Klíč                            | Hodnota                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <your_api_key>                 |

1. Zvolte pro **tělo**možnost **nezpracované** , pak v seznamu možností formátu zvolte **JSON** a pak zahrňte nějaký zástupný obsah **JSON** :

    ```json
    {}
    ```

1. Klikněte na **Odeslat**.

    > [!NOTE]
    > Ve službě byste měli obdržet stav **400 Špatný požadavek** .

1. Proveďte kroky 1-4 znovu, ale tentokrát zadáním koncového bodu požadavků ověříte, že jste obdrželi stejnou **400 chybnou** odpověď na požadavek.

    ```bash
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

> [!NOTE]
> Rozhraní API ještě není možné otestovat pomocí platných dat požadavku, protože to vyžaduje informace specifické pro platformu z klientské mobilní aplikace.
