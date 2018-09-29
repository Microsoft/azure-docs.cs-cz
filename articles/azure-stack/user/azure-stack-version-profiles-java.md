---
title: Použití profilů verzí API v Javě ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API v Javě ve službě Azure Stack.
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
ms.date: 09/28/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: ffd22f3612d55258737cb9c004b2b0f4e9326f07
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452509"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Použití profilů verzí API s Javou v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sada Java SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočty, sítě, úložiště, aplikační služby, a [KeyVault](../../key-vault/key-vault-whatis.md). Sada Java SDK zahrnuje profily rozhraní API včetně závislostí v, který načte správné moduly v souboru .Java, který soubor Pom.xml. Ale můžete přidat více profilů jako závislosti, jako **2018-03-01hybridní**, nebo **nejnovější** jako profil Azure. Pomocí těchto závislostí načte správný modul tak, aby při vytváření vašeho typu prostředku, budete moct vybrat, kterou verzi rozhraní API z těchto profilů, které chcete použít. To umožňuje používat nejnovější verze v Azure, při vývoji na základě aktuální verze rozhraní API pro službu Azure Stack. Pomocí sady Java SDK poskytuje prostředí pro vývojáře skutečně hybridní cloud. Profily rozhraní API v jazyce Java SDK povolit hybridní cloudový vývoj díky ní si můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack.

## <a name="java-and-api-version-profiles"></a>Profily verze Javy a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verze rozhraní API. Profil rozhraní API můžete použít k získání nejnovější, většina stabilní verzi každého typu prostředku v balíčku poskytovatele prostředků.

- Chcete-li používat nejnovější verze všech služeb, použijte **nejnovější** profil jako závislost.
    
   - Pokud chcete používat nejnovější profil, závislost je **com.microsoft.azure**.

   - Chcete-li použít služby kompatibilní s Azure Stack, použijte **com.microsoft.azure.profile\_2018\_03\_01\_hybridní** profilu.
    
      - Toto je třeba zadat do souboru Pom.xml jako závislost, která automaticky načte moduly Pokud zvolíte správné třídy z rozevíracího seznamu stejně jako s .NET.
        
          - Horní části každého modulu, který se zobrazí takto:         
           `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`
             

  - Závislosti vypadat následovně:
     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Konkrétní verze rozhraní API pro typ prostředku ve zprostředkovateli konkrétní prostředek, použití konkrétní verze rozhraní API, které jsou definované pomocí intellisense.

Všimněte si, že můžete kombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-java-sdk"></a>Instalace sady Azure Java SDK

Instalace sady Java SDK, postupujte následovně:

1.  Postupujte podle pokynů oficiální nainstalujte Git. Pokyny najdete v tématu [Začínáme - instalaci Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2.  Oficiální pokyny k instalaci [sady Java SDK](http://zulu.org/download/) a [Maven](https://maven.apache.org/). Správná verze je verze 8 Java Developer Kit. Správné Apache Maven je verze 3.0 nebo vyšší. Proměnná prostředí JAVA_HOME musí nastavit na umístění instalace sady Java Development Kit k dokončení tohoto rychlého startu. Další informace najdete v tématu [vytvoření první funkce pomocí Javy a Mavenu](../../azure-functions/functions-create-first-java-maven.md).

3.  K instalaci správné závislé balíčky, otevřete soubor Pom.xml ve vaši aplikaci v Javě. Přidáte závislost, jak je znázorněno v následujícím kódu:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4.  Balíčky, které je potřeba nainstalovat závisí na verzi profilu, který chcete použít. Názvy balíčků pro profil verze jsou:
    
   - **com.microsoft.Azure.Profile\_2018\_03\_01\_hybridní**
   - **com.microsoft.Azure**
      - **nejnovější**

5.  Pokud není k dispozici, vytvořte předplatné a uložit pro pozdější použití. ID předplatného. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

6.  Vytvoření instančního objektu a uložení ID klienta a tajný kód klienta. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md). Všimněte si, že ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

7.  Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Použití sady Java SDK pro Azure pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny pod tabulkou pro váš operační systém.

| Hodnota                     | Proměnné prostředí | Popis                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID tenanta                 | TENANT_ID            | Výhody služby Azure Stack [ <span class="underline">ID tenanta</span>](../azure-stack-identity-overview.md).                                                          |
| ID klienta                 | CLIENT_ID             | Služba ID instančního objektu aplikace neuloží, když se vytvoří nový instanční objekt služby v předchozí části tohoto dokumentu.                                                                                              |
| ID předplatného           | SUBSCRIPTION_ID      | [ <span class="underline">ID předplatného</span> ](../azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack.                |
| Tajný kód klienta             | HODNOTA CLIENT_SECRET        | Aplikace instančního objektu služby tajný klíč neuloží, když se vytvoří nový instanční objekt služby.                                                                                                                                   |
| Koncový bod Resource Manageru | KONCOVÝ BOD              | Zobrazit [ <span class="underline">koncový bod služby Azure Stack resource manager</span>](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Umístění                  | RESOURCE_LOCATION    | Místní pro Azure Stack                                                                                                                                                                                                |

K vyhledání ID tenanta pro službu Azure Stack, postupujte podle pokynů uvedených [tady](../azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, postupujte takto:

### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku Windows, použijte následující formát:

```shell
Set Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux a systémů založených na systému Unix

V systémech Unix na základě můžete použít následující příkaz:

```shell
Export Azure_Tenant_ID=<Your_Tenant_ID>
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod služby Azure Stack resource manager

Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manageru můžete získat informace o metadatech. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.

Mějte na paměti následující aspekty:

- **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`

Načíst metadata, požadované: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Ukázkový soubor JSON:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

1.  **com.microsoft.Azure.Profile\_2018\_03\_01\_hybridní**: nejnovější profil vytvořené pro Azure Stack. Použijte tento profil služby bude nejvíc kompatibilní se systémem Azure Stack, dokud jsou na razítku. 1808 a další.

2.  **com.microsoft.Azure**: profil, který se skládá z nejnovější verze všech služeb. Použijte nejnovější verze všech služeb.

Další informace o profilech rozhraní API a služby Azure Stack, najdete v článku [souhrn rozhraní API profily](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Využití Azure profil rozhraní API sady Java SDK

Následující kód se ověřuje instanční objekt služby ve službě Azure Stack. Vytvoří token prostřednictvím klienta, ID a základní ověřování, který je specifický pro Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionId(subscriptionId);
            Azure azureStack = Azure.configure().withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionId());
```

To umožňuje použít profil závislosti rozhraní API pro nasazení aplikace úspěšně do služby Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definování funkcí, nastavení prostředí Azure Stack

Zaregistrovat správné koncové body cloudu služby Azure Stack, použijte následující kód:

```java
AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceId", settings.get("audience"));
                    put("activeDirectoryGraphResourceId", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".adminvault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

`getActiveDirectorySettings` Volání v následujícím kódu načte z koncových bodů metadat koncových bodů. Uvádí proměnné prostředí z volání, které tvoří:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml
data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Následující ukázky na Githubu jako reference můžete použít k vytváření řešení pomocí profilů .NET a rozhraní API služby Azure Stack:

  - [Správa skupin prostředků](https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid)

  - [Správa účtů úložiště](https://github.com/viananth/storage-java-manage-storage-accounts/tree/stack/Hybrid)

  - [Správa virtuálního počítače](https://github.com/viananth/compute-java-manage-vm/tree/stack/Hybrid)

### <a name="sample-unit-test-project"></a>Ukázkový projekt testování částí 

1.  Naklonujte úložiště pomocí následujícího příkazu:
    
    `git clone https://github.com/viananth/resources-java-manage-resource-group/tree/stack/Hybrid`

2.  Vytvoření instančního objektu Azure a přiřazení role přístupu k předplatnému. Pokyny k vytvoření instančního objektu najdete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](../azure-stack-create-service-principals.md).

3.  Načtěte následující hodnoty proměnné požadované prostředí:
    
   1.  TENANT_ID
   2.  CLIENT_ID
   3.  HODNOTA CLIENT_SECRET
   4.  SUBSCRIPTION_ID
   5.  ARM_ENDPOINT
   6.  RESOURCE_LOCATION

4.  Nastavte následující proměnné prostředí pomocí informace, které jste získali z instanční objekt vytvoříte pomocí příkazového řádku:
    
   1. Export TENANT_ID = {id vašeho tenanta}
   2. Export CLIENT_ID = {id klienta}
   3. Export CLIENT_SECRET {váš tajný klíč klienta} =
   4. Export SUBSCRIPTION_ID = {id předplatného}
   5. Export ARM_ENDPOINT = {vašich prostředků Azure Stack adresa URL správce}
   6. Export RESOURCE_LOCATION = {umístění služby Azure Stack}

   Ve Windows, použijte **nastavit** místo **exportovat**.

5.  Použít `getactivedirectorysettings` kód pro načtení metadat koncového bodu arm a použití klienta HTTP se nastavit informace o koncovém bodu.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6.  Do souboru pom.xml přidáte závislost níže použít profil 2018-03-01hybridní pro službu Azure Stack. Tato závislost nainstaluje moduly spojený s tímto profilem pro poskytovatele prostředků Compute, sítě, úložiště, trezor klíčů a App Services.
      
   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

8.  Na příkazovém řádku, který byl otevřen k nastavení proměnných prostředí zadejte následující řádek:
    
   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Další postup

Další informace o profilech rozhraní API najdete v tématu:

- [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md)
- [Podporuje profily verze rozhraní API poskytovatele prostředků](azure-stack-profiles-azure-resource-manager-versions.md)
