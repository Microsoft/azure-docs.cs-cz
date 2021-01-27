---
title: 'Rychlý Start: první dotaz .NET Core'
description: V tomto rychlém startu budete postupovat podle pokynů pro povolení balíčků NuGet pro graf prostředků pro .NET Core a spuštění prvního dotazu.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917567"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Rychlý Start: spuštění prvního dotazu na graf prostředku pomocí .NET Core

Prvním krokem při použití Azure Resource graphu je ověření, že jsou nainstalované požadované balíčky pro .NET Core. Tento rychlý Start vás provede procesem přidání balíčků do instalace .NET Core.

Na konci tohoto procesu se balíčky přidají do instalace .NET Core a spustí se první dotaz na diagram prostředku.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Objekt služby Azure, včetně _ClientID_ a _clientSecret_. Pokud nemáte instanční objekt pro použití s grafem prostředků nebo chcete vytvořit nový, podívejte se na téma knihovny pro [správu Azure pro ověřování .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Přeskočte krok pro instalaci balíčků .NET Core, jak to provedeme v dalších krocích.

## <a name="create-the-resource-graph-project"></a>Vytvoření projektu prostředku grafu

Pokud chcete povolit .NET Core dotazovat se na Azure Resource Graph, vytvořte novou konzolovou aplikaci a nainstalujte požadované balíčky.

1. Ověřte, že je nainstalovaný nejnovější .NET Core (aspoň **3.1.5**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializujte novou konzolovou aplikaci .NET Core s názvem "argQuery":

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Změňte adresáře do složky nového projektu a nainstalujte požadované balíčky pro Azure Resource Graph:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Nahraďte výchozí hodnotu `program.cs` následujícím kódem a uložte aktualizovaný soubor:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. Sestavte a publikujte `argQuery` konzolovou aplikaci:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Spusťte nejdříve dotaz na Resource Graph použitím Azure CLI

Díky sestavování a publikování konzolové aplikace .NET Core je čas vyzkoušení jednoduchého dotazu grafu prostředku. Dotaz vrátí prvních pět prostředků Azure s **názvem** a **typem prostředku** každého prostředku.

V každém volání metody `argQuery` jsou použity proměnné, které je třeba nahradit vlastními hodnotami:

- `{tenantId}` – Nahraďte ID tenanta.
- `{clientId}` – Nahraďte ID klienta objektu služby.
- `{clientSecret}` – Nahraďte tajným kódem klienta vašeho instančního objektu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.

1. Změňte adresáře na, `{run-folder}` které jste definovali pomocí předchozího `dotnet publish` příkazu.

1. Spusťte svůj první dotaz na Azure Resource Graph pomocí kompilované konzolové aplikace .NET Core:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Na tento dotaz příklad neposkytuje modifikátor řazení jako `order by`, opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost.

1. Změňte konečný parametr na `argQuery.exe` a změňte dotaz na `order by` vlastnost **Name** :

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Stejně jako u prvního dotazu opakované spouštění tohoto dotazu pravděpodobně poskytne jinou sadu zdrojů na jednu žádost. Pořadí příkazů dotazů je důležité. V tomto příkladu `order by` přichází po `limit`. Toto pořadí příkazů nejprve omezí výsledky dotazu a pak je vyřadí.

1. Změňte konečný parametr na `argQuery.exe` a změňte dotaz na `order by` **název vlastnosti název** a pak `limit` na horních pět výsledků:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Když se konečný dotaz několikrát spustí, předpokládá se, že se nic ve vašem prostředí nemění, vrácené výsledky jsou konzistentní a seřazené podle vlastnosti **Name** , ale pořád se omezí na pět nejlepších výsledků.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat konzolovou aplikaci .NET Core a nainstalované balíčky, můžete to udělat tak, že odstraníte `argQuery` složku projektu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili konzolovou aplikaci .NET Core s požadovanými balíčky grafu prostředků a spustíte svůj první dotaz. Chcete-li získat další informace o jazyku grafu prostředků, přejděte na stránku podrobností dotazovacího jazyka.

> [!div class="nextstepaction"]
> [Získat další informace o dotazovacím jazyku](./concepts/query-language.md)