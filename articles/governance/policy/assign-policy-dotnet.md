---
title: 'Rychlý Start: nové přiřazení zásad s .NET Core'
description: V tomto rychlém startu pomocí .NET Core vytvoříte přiřazení Azure Policy k identifikaci prostředků, které nedodržují předpisy.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91604671"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Rychlý Start: vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí .NET Core

Prvním krokem k porozumění dodržování předpisů v Azure je zjištění stavu vašich prostředků. V tomto rychlém startu vytvoříte přiřazení zásady pro identifikaci virtuálních počítačů, které nepoužívají spravované disky. Po dokončení budete identifikovat virtuální počítače, které _nedodržují předpisy_.

Knihovna .NET Core se používá ke správě prostředků Azure. Tato příručka vysvětluje, jak pomocí knihovny .NET Core pro Azure Policy vytvořit přiřazení zásady.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Objekt služby Azure, včetně _ClientID_ a _clientSecret_. Pokud nemáte instanční objekt pro použití s Azure Policy nebo chcete vytvořit nový, přečtěte si téma [knihovny pro správu Azure pro ověřování .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Přeskočte krok pro instalaci balíčků .NET Core, jak to provedeme v dalších krocích.

## <a name="create-the-azure-policy-project"></a>Vytvoření projektu Azure Policy

Pokud chcete, aby .NET Core mohl spravovat Azure Policy, vytvořte novou konzolovou aplikaci a nainstalujte požadované balíčky.

1. Ověřte, že je nainstalovaný nejnovější .NET Core (aspoň **3.1.8**). Pokud ještě není nainstalovaný, Stáhněte si ho na adrese [dotnet.Microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializujte novou konzolovou aplikaci .NET Core s názvem "policyAssignment":

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Změňte adresáře na novou složku projektu a nainstalujte požadované balíčky pro Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

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
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. Sestavte a publikujte `policyAssignment` konzolovou aplikaci:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Vytvoření přiřazení zásad

V tomto rychlém startu vytvoříte přiřazení zásady a přiřadíte **virtuální počítače auditu, které nepoužívají definici Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Tato definice zásady identifikuje prostředky, které nesplňují podmínky nastavené v definici zásady.

1. Změňte adresáře na, `{run-folder}` které jste definovali pomocí předchozího `dotnet publish` příkazu.

1. Do terminálu zadejte následující příkaz:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Předchozí příkazy používají následující informace:

- `{tenantId}` – Nahraďte ID tenanta.
- `{clientId}` – Nahraďte ID klienta objektu služby.
- `{clientSecret}` – Nahraďte tajným kódem klienta vašeho instančního objektu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.
- **název** – jedinečný název objektu přiřazení zásady. Výše uvedený příklad používá _audit-VM-manageddisks_.
- **DisplayName** – zobrazovaný název přiřazení zásady. V takovém případě použijete _přiřazení audit virtuálních počítačů bez spravovaných disků_.
- **policyDefID** – cesta k definici zásad na základě toho, kterou používáte k vytvoření přiřazení. V tomto případě se jedná o ID _virtuálních počítačů auditu definice zásad, které nepoužívají spravované disky_.
- **Popis** – podrobnější vysvětlení toho, co zásada dělá nebo proč je přiřazena tomuto oboru.
- **Scope** – obor určuje, pro které prostředky nebo skupiny prostředků se toto přiřazení zásady bude uplatňovat. Může být v rozsahu od skupiny pro správu k individuálnímu prostředku. Nezapomeňte nahradit `{scope}` jedním z následujících způsobů:
  - Skupina pro správu: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Formě `/subscriptions/{subscriptionId}`
  - Skupina prostředků: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Partner `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Nyní můžete identifikovat nekompatibilní prostředky, abyste pochopili stav dodržování předpisů vašeho prostředí.

## <a name="identify-non-compliant-resources"></a>Zjištění nevyhovujících prostředků

Teď, když se vytvoří přiřazení zásady, můžete identifikovat prostředky, které nedodržují předpisy.

1. Inicializujte novou konzolovou aplikaci .NET Core s názvem "policyCompliance":

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Změňte adresáře na novou složku projektu a nainstalujte požadované balíčky pro Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

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
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. Sestavte a publikujte `policyAssignment` konzolovou aplikaci:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Změňte adresáře na, `{run-folder}` které jste definovali pomocí předchozího `dotnet publish` příkazu.

1. Do terminálu zadejte následující příkaz:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Předchozí příkazy používají následující informace:

- `{tenantId}` – Nahraďte ID tenanta.
- `{clientId}` – Nahraďte ID klienta objektu služby.
- `{clientSecret}` – Nahraďte tajným kódem klienta vašeho instančního objektu.
- Proměnnou `{subscriptionId}` nahraďte ID předplatného.
- **název** – jedinečný název objektu přiřazení zásady. Výše uvedený příklad používá _audit-VM-manageddisks_.

Výsledky se shodují s tím, `response` co vidíte na kartě **Kompatibilita prostředků** v přiřazení zásady v zobrazení Azure Portal.

## <a name="clean-up-resources"></a>Vyčištění prostředků

- Odstraňte přiřazení zásad _audit virtuálních počítačů bez spravovaných disků_ prostřednictvím portálu. Definice zásad je integrovaná, takže není k dispozici žádná definice k odebrání.

- Pokud chcete odebrat konzolové aplikace .NET Core a nainstalované balíčky, odstraňte `policyAssignment` `policyCompliance` složky projektu a.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přiřadili definici zásady pro identifikaci prostředků, které nedodržují předpisy, ve vašem prostředí Azure.

Další informace o přiřazení definic zásad pro ověření, že jsou nové prostředky kompatibilní, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Vytváření a správa zásad](./tutorials/create-and-manage.md)
