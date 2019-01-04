---
title: Použití profilů verzí API s využitím .NET SDK ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API s využitím .NET v Azure stacku.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 20e96ad7a99fdb8c90f3b7990965d7225aef8be0
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555009"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Použití profilů verzí API s využitím .NET v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sady .NET SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočty, sítě, úložiště, aplikační služby, a [KeyVault](../../key-vault/key-vault-whatis.md). Sady .NET SDK zahrnuje 14 balíčky NuGet. Tyto balíčky je nutné stáhnout do svého řešení projekt pokaždé, když zahrnuje informace o profilu. Však můžete konkrétně stáhnout kterého zprostředkovatele prostředků budete používat pro 2018-03-01hybridní nebo 2017-03-09-profile k optimalizaci paměť pro vaši aplikaci. Každý balíček se skládá z poskytovatele prostředků, příslušné verze rozhraní API a rozhraní API profil, ke kterému patří. Profily rozhraní API v sadě .NET SDK umožňují vývoj pro hybridní cloudové prostředí tím, že můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack pomáhá.

## <a name="net-and-api-version-profiles"></a>Profilů verzí rozhraní .NET a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verze rozhraní API. Profil rozhraní API můžete použít k získání nejnovější, většina stabilní verzi každého typu prostředku v balíčku poskytovatele prostředků.

-   Chcete-li použít nejnovější verzí všech služeb, použijte **nejnovější** profilu balíčků. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Chcete-li použít služby kompatibilní s Azure Stack, použijte **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** nebo **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** balíčky.

    -   Dostupné jsou dva balíčky pro každý poskytovatel prostředků pro každý profil.

    -   Ujistěte se, že **ResourceProvider** část výše uvedených balíčků NuGet se změní na poskytovateli správné.

-   Chcete-li používat nejnovější verzi rozhraní API služby, použijte **nejnovější** profil konkrétního balíčku NuGet. Například, pokud chcete použít **nejnovější verzi rozhraní API** verze výpočetní služby samostatně, použijte **nejnovější** profil **compute** balíčku. **Nejnovější** profilu je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Konkrétní verze rozhraní API pro typ prostředku ve zprostředkovateli konkrétní prostředek, použití konkrétní verze rozhraní API definované uvnitř balíčku.

Můžete sloučit všechny možností ve stejné aplikaci.

## <a name="install-the-azure-net-sdk"></a>Instalace sady Azure .NET SDK

1.  Instalace Gitu. Pokyny najdete v tématu [Začínáme – instalace Git][].

2.  K instalaci správné balíčky NuGet, naleznete v tématu [Vyhledání a instalace balíčku][].

3.  Balíčky, které je potřeba nainstalovat závisí na verzi profilu, který chcete použít. Názvy balíčků pro profil verze jsou:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  K instalaci správné balíčky NuGet pro Visual Studio Code, viz následující odkaz ke stažení [Pokyny pro správce balíčků NuGet][].

5.  Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack][].

6.  Vytvoření instančního objektu a uložení ID klienta a tajný kód klienta. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][]. ID klienta je také označovaný jako ID aplikace, při vytváření instančního objektu.

7.  Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][].

## <a name="prerequisites"></a>Požadavky

Použití sady .NET SDK pro Azure pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny pod tabulkou pro váš operační systém.

| Hodnota                     | Proměnné prostředí   | Popis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta                 | AZURE_TENANT_ID       | Výhody služby Azure Stack [ *ID tenanta*][].                                                                          |
| ID klienta                 | AZURE_CLIENT_ID       | Služba ID instančního objektu aplikace neuloží, když se objekt služby byl vytvořen v předchozí části tohoto článku. |
| ID předplatného           | AZURE_SUBSCRIPTION_ID | [ *ID předplatného* ][] je, jak získat přístup k nabídky ve službě Azure Stack.                                                      |
| Tajný kód klienta             | AZURE_CLIENT_SECRET   | Služba hlavní tajný klíč aplikace neuloží, když se vytvoří nový instanční objekt služby.                                      |
| Koncový bod Resource Manageru | ARM_ENDPOINT           | Zobrazit [ *koncový bod Azure Stack Resource Manager*][].                                                                    |
| Umístění                  | RESOURCE_LOCATION     | Umístění pro službu Azure Stack.

Najít ID Tenanta pro službu Azure Stack, postupujte podle pokynů uvedených [tady](../azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, proveďte následující kroky:

### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku Windows, použijte následující formát:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux a systémů založených na systému Unix

V systémech Unix na základě můžete použít následující příkaz:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod Azure Stack Resource Manageru

Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manageru můžete získat informace o metadatech. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.

Mějte na paměti následující aspekty:

- **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Ukázkový soubor JSON:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: Nejnovější profil vytvořené pro Azure Stack. Použijte tento profil služby bude nejvíc kompatibilní se systémem Azure Stack, dokud jsou na razítku. 1808 a další.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Pokud používáte nižší než sestavení. 1808 razítka, použijte tento profil.

3.  **Nejnovější**: Profil, který se skládá z nejnovější verze všech služeb. Použijte nejnovější verze všech služeb. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

Další informace o profilech rozhraní API a služby Azure Stack, najdete v článku [Přehled rozhraní API profily][].

## <a name="azure-net-sdk-api-profile-usage"></a>Využití Azure .NET SDK API profilu

Následující kód by měla sloužit k vytvoření instance klienta správy prostředků. Podobný kód je možné vytvořit instanci jiný poskytovatel prostředků (jako jsou výpočty, síť a úložiště) klienti. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

`credentials` Parametru ve výše uvedeném kódu je nutné vytvořit instanci klienta. Následující kód vygeneruje ověřovací token ID tenanta a instanční objekt.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
`getActiveDirectoryServiceSettings` Volání v kódu načte z koncového bodu metadat koncových bodů služby Azure Stack. Uvádí proměnné prostředí z volání, které tvoří: 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
To vám umožní používat balíčky NuGet rozhraní API profil nasadit vaše aplikace úspěšně do služby Azure Stack.

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Následující ukázky můžete použít jako odkaz pro vytvoření řešení s využitím .NET a rozhraní API služby Azure Stack profily.
- [Správa skupin prostředků](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Správa účtů úložiště](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Správa virtuálního počítače](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>Další postup

Další informace o profilech rozhraní API najdete v tématu:

- [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md)
- [Podporuje profily verze rozhraní API poskytovatele prostředků](azure-stack-profiles-azure-resource-manager-versions.md)

  [Začínáme – instalace Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Vyhledání a instalace balíčku]: /nuget/tools/package-manager-ui
  [Pokyny pro správce balíčků NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Vytvářet předplatná na nabídky ve službě Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Poskytují aplikace přístup ke službě Azure Stack]: ../azure-stack-create-service-principals.md
  [* tenant ID *]: ../azure-stack-identity-overview.md
  [* předplatné ID *]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* na Azure Stack Resource Manager koncový bod *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Přehled rozhraní API profily]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
