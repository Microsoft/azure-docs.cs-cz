---
title: 'Rychlý Start: Vytvoření skupiny pro správu pomocí .NET Core'
description: V tomto rychlém startu pomocí .NET Core vytvoříte skupinu pro správu, která slouží k uspořádání prostředků do hierarchie prostředků.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 138998fdc23fd8a296ca50093e2952017888041f
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604668"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Rychlý Start: Vytvoření skupiny pro správu pomocí .NET Core

Skupiny pro správu jsou kontejnery, které vám pomůžou spravovat přístup, zásady a dodržování předpisů v různých předplatných. Vytvořte tyto kontejnery, abyste vytvořili efektivní a efektivní hierarchii, která se dá použít s [Azure Policy](../policy/overview.md) a [řízení přístupu na základě rolí Azure](../../role-based-access-control/overview.md). Další informace o skupinách pro správu najdete v tématu [uspořádání prostředků pomocí skupin pro správu Azure](overview.md).

První skupina pro správu vytvořená v adresáři může trvat až 15 minut, než se dokončí. Při nastavování služby skupiny pro správu v Azure pro váš adresář existují procesy, které se spouštějí poprvé. Po dokončení procesu obdržíte oznámení. Další informace najdete v tématu [počáteční nastavení skupin pro správu](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Objekt služby Azure, včetně _ClientID_ a _clientSecret_. Pokud nemáte instanční objekt pro použití s Azure Policy nebo chcete vytvořit nový, přečtěte si téma [knihovny pro správu Azure pro ověřování .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Přeskočte krok pro instalaci balíčků .NET Core, jak to provedeme v dalších krocích.

- Libovolný uživatel Azure AD v tenantovi může vytvořit skupinu pro správu bez oprávnění k zápisu skupiny pro správu, která jsou přiřazená tomuto uživateli, pokud není povolena [ochrana hierarchie](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Tato nová skupina pro správu se stal podřízenou skupinou kořenové skupiny pro správu nebo [výchozí skupině pro správu](./how-to/protect-resource-hierarchy.md#setting---default-management-group) a autorovi je přiděleno přiřazení role "vlastník". Služba skupiny pro správu umožňuje tuto možnost, takže přiřazení rolí není nutné na kořenové úrovni. Žádní uživatelé nemají přístup ke kořenové skupině pro správu při jejím vytvoření. Aby nedocházelo k tomu, že by globální Správci služby Azure AD mohli začít používat skupiny pro správu, umožníme vytváření počátečních skupin pro správu na kořenové úrovni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Nastavení aplikace

Chcete-li povolit .NET Core pro správu skupin pro správu, vytvořte novou konzolovou aplikaci a nainstalujte požadované balíčky.

1. Ověřte, že je nainstalovaný nejnovější .NET Core (aspoň **3.1.8**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializujte novou konzolovou aplikaci .NET Core s názvem "mgCreate":

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Změňte adresáře na novou složku projektu a nainstalujte požadované balíčky pro Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Sestavte a publikujte `mgCreate` konzolovou aplikaci:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Vytvoření skupiny pro správu

V tomto rychlém startu vytvoříte novou skupinu pro správu v kořenové skupině pro správu.

1. Změňte adresáře na, `{run-folder}` které jste definovali pomocí předchozího `dotnet publish` příkazu.

1. Do terminálu zadejte následující příkaz:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Předchozí příkazy používají následující informace:

- `{tenantId}` – Nahraďte ID tenanta.
- `{clientId}` – Nahraďte ID klienta objektu služby.
- `{clientSecret}` – Nahraďte tajným kódem klienta vašeho instančního objektu.
- `{groupID}` – Nahraďte ID nové skupiny pro správu.
- `{displayName}` – Nahraďte popisným názvem vaší nové skupiny pro správu.

Výsledkem je nová skupina pro správu v kořenové skupině pro správu.

## <a name="clean-up-resources"></a>Vyčištění prostředků

- Odstraňte novou skupinu pro správu prostřednictvím portálu.

- Pokud chcete odebrat konzolovou aplikaci .NET Core a nainstalované balíčky, odstraňte `mgCreate` složku projektu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili skupinu pro správu, která bude organizovat svou hierarchii prostředků. Skupina pro správu může obsahovat předplatná nebo jiné skupiny pro správu.

Pokud chcete získat další informace o skupinách pro správu a o tom, jak spravovat hierarchii prostředků, pokračujte tady:

> [!div class="nextstepaction"]
> [Správa prostředků pomocí skupin pro správu](./manage.md)