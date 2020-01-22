---
title: Správa referenčních dat v prostředích C# GA pomocí-Azure Time Series Insights | Microsoft Docs
description: Naučte se spravovat referenční data pro prostředí GA vytvořením vlastní aplikace napsané v C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/16/2020
ms.custom: seodec18
ms.openlocfilehash: 778e65a0076db374cd82fd8b94eb552cb77857f7
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310879"
---
# <a name="manage-ga-reference-data-for-an-azure-time-series-insights-environment-using-c"></a>Správa referenčních dat GA pro Azure Time Series Insights prostředí pomocíC#

Tento článek ukazuje, jak kombinovat C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)a Azure Active Directory, aby programové požadavky rozhraní API [odkazovaly na rozhraní API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)Azure Time Series Insights GA Správa dat.

## <a name="prerequisites"></a>Požadavky

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřízení Azure Time Series Insightsho prostředí GA](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
)

1. [Vytvořte referenční datovou sadu](time-series-insights-add-reference-data-set.md) v rámci vašeho prostředí. Použijte následující schéma referenčních dat:

   | Název klíče | Typ |
   | --- | --- |
   | Uuid | Řetězec | 

1. Nakonfigurujte Azure Time Series Insights prostředí pro Azure Active Directory, jak je popsáno v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Jako **identifikátor URI přesměrování**použijte `http://localhost:8080/`.

1. Nainstalujte požadované závislosti projektu.

1. Níže uvedený ukázkový kód nahraďte každým **#PLACEHOLDER #** a příslušným identifikátorem prostředí.

1. Spusťte `dotnet run` v kořenovém adresáři projektu. Po zobrazení výzvy použijte svůj uživatelský profil pro přihlášení k Azure. 

> [!TIP]
> * Další ukázky kódů C# GA najdete na [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/csharp-tsi-ga-sample).

## <a name="project-dependencies"></a>Závislosti projektu

Doporučuje se použít nejnovější verzi sady Visual Studio a **NETCore. app**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – verze 16.4.2 +
* [NETCore. app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) – verze 2.2.8

Vzorový kód má dvě požadované závislosti:

* MSAL.NET balíček [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -4.7.1.
* Balíček [Newtonsoft. JSON](https://www.nuget.org/packages/Newtonsoft.Json) -12.0.3.

Přidejte balíčky pomocí [NuGet 2.12 +](https://www.nuget.org/):

* `dotnet add package Newtonsoft.Json --version 12.0.3`
* `dotnet add package Microsoft.Identity.Client --version 4.7.1`

Ani

1. Deklarovat `csharp-tsi-msal-ga-sample.csproj` soubor:

    ```XML
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <LangVersion>latest</LangVersion>
        <RootNamespace>csharp-tsi-msal-ga-sample</RootNamespace>
        <RunWorkingDirectory>$(MSBuildThisFileDirectory)</RunWorkingDirectory>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Identity.Client" Version="4.7.1.0" Culture="neutral" PublicKeyToken="0a613f4dd989e8ae" />
        <PackageReference Include="Newtonsoft.Json" Version="12.0.3" />
      </ItemGroup>
    </Project>
    ```
1. Potom spusťte `dotnet restore`.

## <a name="c-sample-code"></a>C#vzorový kód

```csharp
// Copyright (c) Microsoft Corporation.  All rights reserved.

namespace CsharpTsiMsalGaSample
{
    using Microsoft.Identity.Client;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;

    public static class Program
    {
        /**
         * Review the product documentation for detailed configuration steps or skip ahead and configure your environment settings.
         * 
         * https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization
         */

        // Azure Time Series Insights environment configuration
        internal static string EnvironmentFqdn = "#PLACEHOLDER#.env.timeseries.azure.com";
        internal static string EnvironmentReferenceDataSetName = "#PLACEHOLDER#";

        // Azure Active Directory application configuration
        internal static string AadClientApplicationId = "#PLACEHOLDER#";
        internal static string[] AadScopes = new string[] { "https://api.timeseries.azure.com//user_impersonation" };
        internal static string AadRedirectUri = "http://localhost:8080/";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadAuthenticationAuthority.StartsWith("#PLACEHOLDER#"))
            {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started"} to update the values of 'AadClientApplicationId', 'AadScopes', 'AadRedirectUri', and 'AadAuthenticationAuthority'.");
            }

            /**
             * MSAL.NET configuration. Review the product documentation for more information about MSAL.NET authentication options.
             * 
             * https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/
             */

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(AadClientApplicationId)
                .WithRedirectUri(AadRedirectUri)
                .WithAuthority(AadAuthenticationAuthority)
                .Build();

            AuthenticationResult result = await app
                .AcquireTokenInteractive(AadScopes)
                .ExecuteAsync();

            Console.WriteLine("MSAL Authentication Token Acquired: {0}", result.AccessToken);
            Console.WriteLine("");
            return result.AccessToken;
        }

        // System.Net.HttpClient helper to wrap HTTP POST made to the GA Reference Data API
        private static async Task<HttpResponseMessage> AsyncHttpPostRequestHelper(HttpClient httpClient, string input)
        {
             if (EnvironmentFqdn == "#PLACEHOLDER#" || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
             {
                throw new Exception($"Use the link {"https://docs.microsoft.com/azure/time-series-insights/time-series-insights-authentication-and-authorization"} to update the values of 'EnvironmentFqdn' and 'EnvironmentReferenceDataSetName'.");
             }

             Console.WriteLine("HTTP JSON Request Body: {0}", input);
             Console.WriteLine("");
             HttpContent requestBody = new StringContent(input, Encoding.UTF8, "application/json");

             Uri uri = new UriBuilder("https", EnvironmentFqdn)
             {
                Path = $"referencedatasets/{EnvironmentReferenceDataSetName}/$batch",
                Query = "api-version=2016-12-12"
             }.Uri;
                
             Console.WriteLine("Making HTTP POST to URI: {0}", uri);
             Console.WriteLine("");

             HttpResponseMessage response = await httpClient.PostAsync(uri, requestBody);
             if (response.IsSuccessStatusCode)
             {
                var jsonString = await response.Content.ReadAsStringAsync();
                var r = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", r);
                Console.WriteLine("");
                return response;
             }

             return null;
        }

        private static async Task TsiMsalGaSample()
        {
            Console.WriteLine("Beginning demo...");
            Console.WriteLine("");
            Console.WriteLine("The following samples assume a single Key Name (uuid) with String type...");
            Console.WriteLine("");

            string accessToken = await AcquireAccessTokenAsync();
            var httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + accessToken);

            {   
                // CREATE reference data
                Console.WriteLine("CREATE reference data example...");
                Console.WriteLine("");
                string createInput = @"
                    {
                        ""put"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""floor"": 2
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";


                var createResponse = await AsyncHttpPostRequestHelper(httpClient, createInput);

                // READ reference data
                Console.WriteLine("READ reference data example...");
                Console.WriteLine("");
                string readInput = @"
                    {
                        ""get"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""White"",
                                ""desc"": ""example""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""color"": ""Red"",
                                ""maxSpeed"": 5
                            }
                        ]
                    }";

                var readResponse = await AsyncHttpPostRequestHelper(httpClient, readInput);

                // UPDATE reference data
                Console.WriteLine("UPDATE reference data example...");
                Console.WriteLine("");
                string updateInput = @"
                    {
                        ""patch"": [
                            {
                                ""uuid"": ""Fan1"",
                                ""color"": ""Red""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan3"",
                                ""desc"": ""Example""
                            }
                        ]
                    }";

                var inputResponse = await AsyncHttpPostRequestHelper(httpClient, updateInput);

                // DELETE reference data
                Console.WriteLine("DELETE reference data example...");
                Console.WriteLine("");
                string deleteInput = @"
                    {
                        ""delete"": [
                            {
                                ""uuid"": ""Fan1""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""color"": ""Orange""
                            },
                            {
                                ""uuid"": ""Fan2"",
                                ""desc"": ""Blue""
                            }
                        ]
                    }";

                var deleteResponse = await AsyncHttpPostRequestHelper(httpClient, deleteInput);
            }
        }

        internal static void Main(string[] args)
        {
            Task.Run(async () => await TsiMsalGaSample()).Wait();
        }
    }
}
```

## <a name="summary"></a>Souhrn

Vzorový kód výše znázorňuje následující funkce:

* Získání přístupového tokenu pomocí [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**
* Sekvenční vytváření, čtení, aktualizace a odstraňování operací s [referencí GA Správa dat API](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
* Běžné kódy odpovědí včetně [běžných kódů chyb](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api#validation-and-error-handling).
    
    Reference Správa dat API každou položku zpracuje jednotlivě a chyba s jednou položkou nebrání úspěšnému dokončení ostatních položek. Pokud třeba vaše žádost obsahuje 100 položek a jedna položka obsahuje chybu, napíší se položky 99 a jedna z nich se odmítne.

## <a name="next-steps"></a>Další kroky

- Přečtěte si referenční dokumentaci k [rozhraní API Správa dat reference](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) pro ga.
