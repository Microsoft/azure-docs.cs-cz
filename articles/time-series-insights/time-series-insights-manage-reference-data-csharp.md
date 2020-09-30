---
title: Správa referenčních dat v prostředích GA pomocí C#-Azure Time Series Insights | Microsoft Docs
description: Naučte se spravovat referenční data pro prostředí GA vytvořením vlastní aplikace napsané v jazyce C#.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: fb0c29fb68d0bcd2405cc031008c1c8e5035476f
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569392"
---
# <a name="manage-reference-data-for-an-azure-time-series-insights-gen-1-environment-using-c-sharp"></a>Správa referenčních dat pro prostředí Azure Time Series Insights Gen 1 pomocí jazyka C Sharp

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek ukazuje, jak kombinovat C#, [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)a Azure Active Directory, aby programové požadavky rozhraní API [ODKAZOVALy na rozhraní](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api)api Azure Time Series Insights Gen 1 Správa dat.

> [!TIP]
> Podívejte se na ukázky kódů GA C# na adrese [https://github.com/Azure-Samples/Azure-Time-Series-Insights](https://github.com/Azure-Samples/Azure-Time-Series-Insights/tree/master/gen1-sample/csharp-tsi-gen1-sample) .

## <a name="summary"></a>Souhrn

Vzorový kód níže znázorňuje následující funkce:

* Získání přístupového tokenu pomocí [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) **PublicClientApplication**
* Sekvenční vytváření, čtení, aktualizace a odstraňování operací proti [referenčnímu rozhraní Správa dat API](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api)pro obecné 1.
* Běžné kódy odpovědí včetně [běžných kódů chyb](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api#validation-and-error-handling).

    Reference Správa dat API každou položku zpracuje jednotlivě a chyba s jednou položkou nebrání úspěšnému dokončení ostatních položek. Pokud třeba vaše žádost obsahuje 100 položek a jedna položka obsahuje chybu, napíší se položky 99 a jedna z nich se odmítne.

## <a name="prerequisites-and-setup"></a>Požadavky a instalace

Před kompilací a spuštěním ukázkového kódu proveďte následující kroky:

1. [Zřízení Azure Time Series Insightsho prostředí s 1. generace](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started
)

1. [Vytvořte referenční datovou sadu](time-series-insights-add-reference-data-set.md) v rámci vašeho prostředí. Použijte následující schéma referenčních dat:

   | Název klíče | Typ |
   | --- | --- |
   | uuid | Řetězec |

1. Nakonfigurujte Azure Time Series Insights prostředí pro Azure Active Directory, jak je popsáno v tématu [ověřování a autorizace](time-series-insights-authentication-and-authorization.md). Použijte `http://localhost:8080/` jako **identifikátor URI přesměrování**.

1. Nainstalujte požadované závislosti projektu.

1. Níže uvedený ukázkový kód nahraďte každým **#PLACEHOLDER #** a příslušným identifikátorem prostředí.

1. Spusťte `dotnet run` v kořenovém adresáři vašeho projektu. Po zobrazení výzvy použijte svůj uživatelský profil pro přihlášení k Azure.

## <a name="project-dependencies"></a>Závislosti projektu

Doporučuje se použít nejnovější verzi sady Visual Studio a **NETCore. app**:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) – verze 16.4.2 +
* [NETCore. app](https://www.nuget.org/packages/Microsoft.NETCore.App/2.2.8) – verze 2.2.8

Vzorový kód má dvě požadované závislosti:

* MSAL.NET balíček [Microsoft. identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) -4.7.1.
* [Newtonsoft.Jsbalíček on](https://www.nuget.org/packages/Newtonsoft.Json) -12.0.3.

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

## <a name="c-sample-code"></a>Ukázkový kód C#

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
        internal static string AadTenantName = "#PLACEHOLDER#";
        internal static string AadAuthenticationAuthority = "https://login.microsoftonline.com/" + AadTenantName + ".onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/";

        private static async Task<string> AcquireAccessTokenAsync()
        {
            if (AadClientApplicationId == "#PLACEHOLDER#" || AadScopes.Length == 0 || AadRedirectUri == "#PLACEHOLDER#" || AadTenantName.StartsWith("#PLACEHOLDER#"))
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
             if (EnvironmentFqdn.StartsWith("#PLACEHOLDER#") || EnvironmentReferenceDataSetName == "#PLACEHOLDER#")
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
                var jsonStringTransferObject = JsonConvert.DeserializeObject<object>(jsonString);
                Console.WriteLine("HTTP JSON Response Body: {0}", jsonStringTransferObject);
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

## <a name="next-steps"></a>Další kroky

* Přečtěte si referenční dokumentaci k [rozhraní API Správa dat reference](https://docs.microsoft.com/rest/api/time-series-insights/gen1-reference-data-api) pro obecné 1.
