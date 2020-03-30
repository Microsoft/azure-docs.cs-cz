---
title: Připojení k rozhraní API Azure Media Services v3 – rozhraní .NET
description: Tento článek ukazuje, jak se připojit k rozhraní API Media Services v3 s rozhraním .NET.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269806"
---
# <a name="connect-to-media-services-v3-api---net"></a>Připojení k rozhraní API Media Services v3 - .NET

Tento článek ukazuje, jak se připojit k Azure Media Services v3 .NET SDK pomocí metody přihlášení instančního objektu.

## <a name="prerequisites"></a>Požadavky

- [Vytvořte účet mediálních služeb](create-account-cli-how-to.md). Nezapomeňte si zapamatovat název skupiny prostředků a název účtu Služby Media Services.
- Nainstalujte nástroj, který chcete použít pro vývoj rozhraní .NET. Kroky v tomto článku ukazují, jak používat [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Můžete použít Visual Studio Kód, naleznete v [tématu Práce s C#](https://code.visualstudio.com/docs/languages/csharp). Nebo můžete použít jiný editor kódu.

> [!IMPORTANT]
> Zkontrolujte [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio. 
1. V nabídce **Soubor** klepněte na položku **Nový** > **projekt**. 
1. Vytvořte aplikaci konzoly **.NET Core.**

Ukázková aplikace v tomto `netcoreapp2.0`tématu cílí na . Kód používá 'asynchronní hlavní', který je k dispozici počínaje C# 7.1. Viz tento [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) pro více informací.

## <a name="add-required-nuget-packages"></a>Přidání požadovaných balíčků NuGet

1. V sadě Visual Studio vyberte **nástroje** > **NuGet Správce balíčků** > **NuGet Manager Console**.
2. V okně **konzoly** Správce `Install-Package` balíčků přidejte pomocí příkazu následující balíčky NuGet. Například, `Install-Package Microsoft.Azure.Management.Media`.

|Balíček|Popis|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Chcete-li se ujistit, že používáte nejnovější balíček Služby Azure Media Services, podívejte se na [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL ověřování knihovna pro Azure SDK pro NET|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Čtení konfiguračních hodnot z proměnných prostředí a místních souborů JSON|
|`Microsoft.Extensions.Configuration.Json`|Čtení konfiguračních hodnot z proměnných prostředí a místních souborů JSON
|`WindowsAzure.Storage`|Sada SDK úložiště|

## <a name="create-and-configure-the-app-settings-file"></a>Vytvoření a konfigurace souboru nastavení aplikace

### <a name="create-appsettingsjson"></a>Vytvoření souboru appsettings.json

1. Přejít **Obecný** > **textový soubor**.
1. Pojmenujte jej "appsettings.json".
1. Nastavte vlastnost "Kopírovat do výstupního adresáře" souboru JSON na "Kopírovat, pokud novější" (tak, aby aplikace mohla přistupovat k němu při publikování).

### <a name="set-values-in-appsettingsjson"></a>Nastavení hodnot v souboru appsettings.json

Spusťte příkaz, `az ams account sp create` jak je popsáno v [přístupových api](access-api-cli-how-to.md). Příkaz vrátí json, který byste měli zkopírovat do "appsettings.json".
 
## <a name="add-configuration-file"></a>Přidání konfiguračního souboru

Pro větší pohodlí přidejte konfigurační soubor, který je zodpovědný za čtení hodnot z "appsettings.json".

1. Přidejte do projektu novou třídu .cs. Pojmenujte ji `ConfigWrapper`. 
1. Vložte do tohoto souboru následující kód (tento příklad `ConsoleApp1`předpokládá, že máte obor názvů ).

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

## <a name="connect-to-the-net-client"></a>Připojení ke klientovi .NET

Pokud chcete začít používat rozhraní Media Services API se sadou .NET SDK, musíte vytvořit objekt **AzureMediaServicesClient**. K vytvoření tohoto objektu, musíte zadat přihlašovací údaje, aby se klient mohl připojit k Azure pomocí Azure AD. V níže uvedeném kódu vytvoří funkce GetCredentialsAsync objekt ServiceClientCredentials na základě pověření zadaných v místním konfiguračním souboru.

1. Otevřete `Program.cs`.
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

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
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

## <a name="next-steps"></a>Další kroky

- [Kurz: Nahrávání, kódování a streamování videí - .NET](stream-files-tutorial-with-api.md) 
- [Kurz: Streamujte živě s Mediálními službami v3 - .NET](stream-live-tutorial-with-api.md)
- [Kurz: Analýza videí pomocí mediálních služeb v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Vytvoření vstupu úlohy z místního souboru - .NET](job-input-from-local-file-how-to.md)
- [Vytvoření vstupu úlohy z adresy URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Zakódovat pomocí vlastní transformace - .NET](customize-encoder-presets-how-to.md)
- [Použití dynamického šifrování AES-128 a služby doručování klíčů - .NET](protect-with-aes128.md)
- [Použití dynamického šifrování DRM a služby doručování licencí - .NET](protect-with-drm.md)
- [Získání podpisového klíče ze stávajících zásad - .NET](get-content-key-policy-dotnet-howto.md)
- [Vytváření filtrů pomocí mediálních služeb - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Pokročilé příklady videa na vyžádání azure functions v2 s Mediální službou v3](https://aka.ms/ams3functions)

## <a name="see-also"></a>Viz také

* [Reference k .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* Další příklady kódu naleznete [v ukládat ukázky sady .NET SDK.](https://github.com/Azure-Samples/media-services-v3-dotnet)
