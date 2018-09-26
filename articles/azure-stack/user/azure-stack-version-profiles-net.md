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
ms.date: 09/25/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 35329468ee01d5b70d654c1eb4a908db9d3fcb5d
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184753"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Použití profilů verzí API s využitím .NET v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sady .NET SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočty, sítě, úložiště, aplikační služby, a [KeyVault](../../key-vault/key-vault-whatis.md). Sady .NET SDK zahrnuje 14 balíčky NuGet, které je nutné stáhnout do svého řešení projekt pokaždé, když, které zahrnují informace o profilu. Však můžete konkrétně stáhnout kterého zprostředkovatele prostředků budete používat pro 2018-03-01hybridní nebo 2017-03-09-profile k optimalizaci paměť pro vaši aplikaci. Každý balíček se skládá z poskytovatele prostředků, příslušné verze rozhraní API a rozhraní API profil, ke kterému patří. Profily rozhraní API v sadě .NET SDK umožňují vývoj pro hybridní cloudové prostředí tím, že můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack pomáhá.

## <a name="net-and-api-version-profiles"></a>Profilů verzí rozhraní .NET a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verze rozhraní API. Profil rozhraní API můžete použít k získání nejnovější, většina stabilní verzi každého typu prostředku v balíčku poskytovatele prostředků.

-   Chcete-li použít nejnovější verzí všech služeb, použijte **nejnovější** profilu balíčků. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Chcete-li použít služby kompatibilní s Azure Stack, použijte **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** nebo **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** balíčky.

    -   Dostupné jsou dva balíčky pro každý poskytovatel prostředků pro každý profil.

    -   Ujistěte se, že **ResourceProvider** část výše uvedených balíčků NuGet se změní na poskytovateli správné.

-   Chcete-li používat nejnovější verzi rozhraní API služby, použijte **nejnovější** profil konkrétního balíčku NuGet. Například, pokud chcete použít **nejnovější verzi rozhraní API** verze výpočetní služby samostatně, použijte **nejnovější** profil **compute** balíčku. **Nejnovější** profilu je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Konkrétní verze rozhraní API pro typ prostředku ve zprostředkovateli konkrétní prostředek, použití konkrétní verze rozhraní API definované uvnitř balíčku.

Všimněte si, že můžete kombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-net-sdk"></a>Instalace sady Azure .NET SDK

1.  Instalace Gitu. Pokyny najdete v tématu [Začínáme – instalace Git][].

2.  K instalaci správné balíčky NuGet, naleznete v tématu [Vyhledání a instalace balíčku][].

3.  Balíčky, které je potřeba nainstalovat závisí na verzi profilu, který chcete použít. Název balíčku pro profil verze jsou:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  K instalaci správné balíčky NuGet pro Visual Studio Code, viz následující odkaz ke stažení [Pokyny pro správce balíčků NuGet][].

5.  Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack][].

6.  Vytvoření instančního objektu a uložení ID klienta a tajný kód klienta. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][]. Všimněte si, že ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

7.  Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][].

## <a name="prerequisites"></a>Požadavky

Použití sady .NET SDK pro Azure pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny pod tabulkou pro váš operační systém.

| Hodnota                     | Proměnné prostředí   | Popis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta                 | AZURE_TENANT_ID       | Výhody služby Azure Stack [ *ID tenanta*][].                                                                          |
| ID klienta                 | AZURE_CLIENT_ID       | Služba ID instančního objektu aplikace neuloží, když se objekt služby byl vytvořen v předchozí části tohoto článku. |
| ID předplatného           | AZURE_SUBSCRIPTION_ID | [ *ID předplatného* ][] je, jak získat přístup k nabídky ve službě Azure Stack.                                                      |
| Tajný kód klienta             | AZURE_CLIENT_SECRET   | Služba hlavní tajný klíč aplikace neuloží, když se vytvoří nový instanční objekt služby.                                      |
| Koncový bod Resource Manageru | ARM_ENDPOINT           | Zobrazit [ *koncový bod služby Azure Stack resource manager*][].                                                                    |

Najít ID Tenanta pro službu Azure Stack, postupujte podle pokynů uvedených [tady](../azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, postupujte takto:

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

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod služby Azure Stack resource manager

Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manageru můžete získat informace o metadatech. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.

Mějte na paměti následující aspekty:

- **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` pro načtení metadat vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: nejnovější profil vytvořené pro Azure Stack. Použijte tento profil služby bude nejvíc kompatibilní se systémem Azure Stack, dokud jsou na razítku. 1808 a další.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: Pokud používáte nižší než sestavení. 1808 razítka, použít tento profil.

3.  **Nejnovější**: profil, který se skládá z nejnovější verze všech služeb. Použijte nejnovější verze všech služeb. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

Další informace o profilech rozhraní API a služby Azure Stack, najdete v článku [Přehled rozhraní API profily][].

## <a name="azure-net-sdk-api-profile-usage"></a>Využití Azure .NET SDK API profilu

Následující kód by měla sloužit k vytvoření instance profil klienta. Tento parametr je jen pro Azure Stack nebo jiných privátních cloudů. Global Azure už má tato nastavení ve výchozím nastavení.

Následující kód je potřebné k ověření objektu služby ve službě Azure Stack. Vytvoří token prostřednictvím klienta, ID a základní ověřování, který je specifický pro Azure Stack.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

To vám umožní používat balíčky NuGet rozhraní API profil nasadit vaše aplikace úspěšně do služby Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definování funkcí, nastavení prostředí Azure Stack

K ověřování instančního objektu služby pro prostředí Azure Stack, použijte následující kód:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Tím se přepíše inicializace klienta služby pro ověření do služby Azure Stack.

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Můžete použít následující ukázek, které najdete v úložišti GitHub jako referenční informace pro vytváření řešení pomocí profilů .NET a rozhraní API služby Azure Stack.

-   [Projekt testů pro virtuální počítač, virtuální síť, skupiny prostředků a účtu úložiště][]
-   Správa virtuálních počítačů pomocí .NET

### <a name="sample-unit-test-project"></a>Ukázkový projekt testování částí 

1.  Naklonujte úložiště pomocí následujícího příkazu:

    `git clone <https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject>`

2.  Vytvoření instančního objektu Azure a přiřazení role přístupu k předplatnému. Pokyny k vytvoření instančního objektu najdete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem][].

3.  Načte následující požadované hodnoty:

    1.  ID tenanta
    2.  ID klienta
    3.  Tajný kód klienta
    4.  ID předplatného
    5.  Koncový bod Resource Manageru

4.  Nastavte následující proměnné prostředí pomocí informace, které jste získali z instanční objekt že vytvoříte pomocí příkazového řádku:

    1.  Export AZURE_TENANT_ID = {id vašeho tenanta}
    2.  Export AZURE_CLIENT_ID = {id klienta}
    3.  Export AZURE_CLIENT_SECRET = {váš tajný klíč klienta}
    4.  Export AZURE_SUBSCRIPTION_ID = {id předplatného}
    5.  Export ARM_ENDPOINT = {vašich prostředků Azure Stack adresa URL správce}

   Ve Windows, použijte **nastavit** místo **exportovat**.

5.  Ujistěte se, že umístění proměnná je nastavená na umístění vaší služby Azure Stack. Například místní = "local".

6.  Nastavte vlastní přihlašovací údaje, které vám umožní ověření do služby Azure Stack. Všimněte si, že tuto část kódu je zahrnuta v této ukázce ve složce autorizace.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Pokud používáte Azure Stack pro přepsání inicializace klienta služby pro ověření do služby Azure Stack, přidejte následující kód. Všimněte si, že část kódu je již zahrnut v této ukázce ve složce autorizace.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  Pomocí Správce balíčků NuGet, vyhledejte "2018-03-01hybridní" a nainstalujte balíčky spojený s tímto profilem pro poskytovatele prostředků Compute, sítě, úložiště, trezor klíčů a App Services.

2.  V rámci každého úkolu v souboru .cs nastavte parametry, které jsou nezbytné pro práci s Azure Stack. Příklad následujícím způsobem pro úlohu `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Klikněte pravým tlačítkem myši na každou úlohu a vyberte **spuštění testu**.

    1.  Zelené zaškrtnutí na okno na straně podokna upozorní, že každý úkol vytvořil úspěšně založené na parametrech daných. Zkontrolujte prosím, ujistěte se, že byly úspěšně vytvořeny prostředky předplatné služby Azure Stack.

    2.  Další informace o tom, jak spustit testy jednotky, najdete v části [spouštění testů jednotek pomocí Průzkumníka testů.][]

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
  [* na Azure Stack resource manager koncový bod *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Přehled rozhraní API profily]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Projekt testů pro virtuální počítač, virtuální síť, skupiny prostředků a účtu úložiště]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem]: ../azure-stack-create-service-principals.md
  [Spouštění testů jednotek pomocí Průzkumníka testů.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
