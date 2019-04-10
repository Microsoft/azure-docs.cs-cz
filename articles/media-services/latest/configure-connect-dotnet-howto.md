---
title: Připojení k rozhraní API služby Azure Media Services v3 – .NET
description: Zjistěte, jak se připojit k rozhraní API služby Media Services v3 s .NET.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 8f8a1434af768180e34afcaacd6e92ab402ad8cd
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59361235"
---
# <a name="connect-to-media-services-v3-api---net"></a>Připojení k rozhraní API služby Media Services v3 – .NET

Tento článek popisuje, jak se připojit k .NET SDK služby Azure Media Services v3 pomocí hlavního přihlášení na metodu služby.

## <a name="prerequisites"></a>Požadavky

- [Vytvoření účtu Media Services](create-account-cli-how-to.md). Ujistěte se, že si pamatovat název skupiny prostředků a název účtu Media Services
- Nainstalujte nástroj, který chcete použít pro vývoj na platformě .NET. Kroky v tomto článku ukazují, jak používat [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Můžete použít Visual Studio Code, naleznete v tématu [práce s C# ](https://code.visualstudio.com/docs/languages/csharp). Nebo můžete použít editor odlišný kód.

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio. 
1. Z **souboru** nabídky, klikněte na tlačítko **nový** > **projektu**. 
1. Vytvoření **.NET Core** konzolové aplikace.

Ukázková aplikace v tomto tématu, zaměřuje `netcoreapp2.0`. Kód používá "asynchronní hlavní", který je k dispozici od verze C# 7.1. Najdete v tomto [blogu](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) další podrobnosti.

## <a name="add-required-nuget-packages"></a>Přidat požadované balíčky NuGet

1. V sadě Visual Studio, vyberte **nástroje** > **Správce balíčků NuGet** > **konzoly Správce NuGet**.
2. V **Konzola správce balíčků** okno, použijte `Install-Package` příkaz pro přidání následující balíčky NuGet. Například, `Install-Package Microsoft.Azure.Management.Media`.

|Balíček|Popis|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Chcete-li mít jistotu, že používáte nejnovější balíček Azure Media Services, zkontrolujte [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|Knihovny ověřování ADAL pro sadu Azure SDK pro NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Čtení hodnoty konfigurace z proměnné prostředí a místní soubory JSON|
|`Microsoft.Extensions.Configuration.Json`|Čtení hodnoty konfigurace z proměnné prostředí a místní soubory JSON
|`WindowsAzure.Storage`|Sada SDK úložiště|

## <a name="create-and-configure-the-app-settings-file"></a>Vytvoření a konfigurace v souboru nastavení aplikace

### <a name="create-appsettingsjson"></a>Create appsettings.json

1. Přejděte go **Obecné** > **textový soubor**.
1. Pojmenujte ji "appsettings.json".
1. Nastavte vlastnost "Kopírovat do výstupního adresáře" soubor .json na hodnotu kopírovat, pokud je novější"(tak, že aplikace je možné k němu při publikování).

### <a name="set-values-in-appsettingsjson"></a>Nastavte hodnoty v souboru appsettings.json

Spustit `az ams account sp create` příkaz, jak je popsáno v [přístup k rozhraním API](access-api-cli-how-to.md). Příkaz vrátí json, který by měl zkopírovat do vaší "appsettings.json".
 
## <a name="add-configuration-file"></a>Přidání konfiguračního souboru

Pro usnadnění práce přidejte konfigurační soubor, který je zodpovědný za čtení hodnot z "appsettings.json".

1. Přidejte novou třídu .cs do projektu. Pojmenujte ji `ConfigWrapper`. 
1. Vložte následující kód v tomto souboru (Tento příklad předpokládá, že máte obor názvů je `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>Připojte se ke klientovi .NET

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V následujícím kódu GetCredentialsAsync funkce vytvoří objekt ServiceClientCredentials na základě pověření v místním konfiguračním souboru.

1. Otevřít `Program.cs`.
1. Vložte následující kód:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>Další postup

- [Kurz: Nahrávání, kódování a streamování videí – .NET](stream-files-tutorial-with-api.md) 
- [Kurz: Live Stream pomocí Media Services v3 – .NET](stream-live-tutorial-with-api.md)
- [Kurz: Analýza videa pomocí Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Vytvoření vstupní úlohy z místního souboru – .NET](job-input-from-local-file-how-to.md)
- [Vytvoření vstupní úlohy z adresy URL HTTPS – .NET](job-input-from-http-how-to.md)
- [Kódování pomocí vlastní transformace – .NET](customize-encoder-presets-how-to.md)
- [Používat dynamické šifrování AES-128 a služba doručování klíčů – .NET](protect-with-aes128.md)
- [DRM dynamického šifrování a licence doručování služba – .NET](protect-with-drm.md)
- [Získejte podpisový klíč ze stávající zásady – .NET](get-content-key-policy-dotnet-howto.md)
- [Vytváření filtrů pomocí Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Pokročilé videa na vyžádání příklady Azure Functions a 2 služby Media Services v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Další informace najdete v tématech

[.NET – referenční informace](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
