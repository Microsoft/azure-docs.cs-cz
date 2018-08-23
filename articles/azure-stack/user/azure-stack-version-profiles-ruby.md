---
title: Pomocí profilů verzí rozhraní API v Ruby ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API pomocí Ruby ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: a000a54f79e479567168992cdd0786eb9e8b5c32
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055245"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Použití profilů verzí API pomocí Ruby ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Profilů verzí rozhraní API a Ruby

Ruby SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočetní prostředky, virtuální sítě a úložiště dat pomocí jazyka Ruby. Profily rozhraní API v Ruby SDK umožňují vývoj pro hybridní cloudové prostředí tím, že můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack pomáhá.

Profil rozhraní API je kombinací poskytovatelů prostředků a verze aktualizace service. Profil rozhraní API můžete kombinovat různé typy zdrojů.

 - Chcete-li použít nejnovější verzí všech služeb, použijte **nejnovější** profilu gem kumulativní sadu Azure SDK.
 - Chcete-li použít služby kompatibilní s Azure Stack, použijte **V2017_03_09** profilu gem kumulativní sadu Azure SDK.
 - Chcete-li používat nejnovější verzi rozhraní api služby, použijte **nejnovější** profilu konkrétní gem. Například pokud chcete používat nejnovější verzi rozhraní api služby compute samostatně, použijte **nejnovější** profil **Compute** gem.
 - Pokud chcete použít konkrétní verzi rozhraní api pro službu, použijte konkrétní verze rozhraní API definované uvnitř gem.

> [!Note]   
> Můžete sloučit všechny možností ve stejné aplikaci.

## <a name="install-the-azure-ruby-sdk"></a>Nainstalujte si Azure Ruby SDK

 - Oficiální podle pokynů k instalaci [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Oficiální podle pokynů k instalaci [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Při instalaci vyberte **Ruby přidejte do proměnné PATH**
    - Při instalaci Ruby po zobrazení výzvy instalaci Dev kit.
    - Dále nainstalujte například položky bundler pomocí následujícího příkazu:  
      `Gem install bundler`
 - Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru jsou [tady](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Vytvoření instančního objektu a uložit jeho ID a tajný klíč. Pokyny k vytvoření instančního objektu pro Azure Stack se [tady](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Zajistěte, aby že váš objekt služby má role Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby jsou [tady](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Instalace balíčků rubygem

Azure rubygem balíčky můžete nainstalovat přímo.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Mějte na paměti Azure Resource Manageru Ruby SDK je ve verzi preview a budou mít nejnovější rozhraní změny v budoucích verzích. Rozbíjející změny může znamenat vyšší číslo v podverze.

## <a name="usage-of-the-azuresdk-gem"></a>Použití nástroje azure_sdk gem

Nástroj gem azure_sdk, je souhrn všech podporovaných gems v Ruby SDK. Tento nástroj gem se skládá z **nejnovější** profil, který podporuje nejnovější verzi všech služeb. Představuje systémovou správou verzí profilu **V2017_03_09** profil, který je sestaven pro Azure Stack.

Souhrn gem azure_sdk můžete nainstalovat pomocí následujícího příkazu:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Požadavek

Chcete-li používat sadu Azure SDK pro Ruby pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro váš operační systém na nastavení proměnné prostředí. 

| Hodnota | Proměnné prostředí | Popis | 
| --- | --- | --- | --- |
| ID tenanta | AZURE_TENANT_ID | Výhody služby Azure Stack [ID tenanta](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID klienta | AZURE_CLIENT_ID | Služba ID instančního objektu aplikace neuloží, když se vytvoří nový instanční objekt služby v předchozí části tohoto dokumentu.  |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack. |
| Tajný kód klienta | AZURE_CLIENT_SECRET | Aplikace instančního objektu služby tajný klíč neuloží, když se vytvoří nový instanční objekt služby. |
| Koncový bod Resource Manageru | ARM_ENDPOINT | Zobrazit [The Azure Stack resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod služby Azure Stack resource manager

Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manageru můžete získat informace o metadatech. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.

  > [!Note]  
  > **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  Ukázkový soubor JSON:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>Nastavení proměnných prostředí

**Microsoft Windows**  
Chcete-li nastavit proměnné prostředí ve Windows příkazový řádek, použijte následující formát:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux a systémů založených na systému Unix**  
V systémech Unix na základě můžete například použít příkaz:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

Souhrn gem azure_sdk má následující dva profily:

1. **V2017_03_09**  
  Profil vytvořené pro Azure Stack. Použijte tento profil služby nejvíce kompatibilní s Azure Stack.
2. **nejnovější**  
  Profil se skládá z nejnovější verze všech služeb. Použijte nejnovější verze všech služeb.

Další informace o profilech rozhraní API a služby Azure Stack, najdete v článku [souhrn rozhraní API profily](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Profil využití Azure Ruby SDK API

Následující řádky by měla sloužit k vytvoření instance profil klienta. Tento parametr je jen pro Azure Stack nebo jiných privátních cloudů. Global Azure už má tato nastavení ve výchozím nastavení.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

Profil klienta můžete použít pro přístup k poskytovateli jednotlivých prostředků, jako jsou výpočty, úložiště a síť.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definovat AzureStack prostředí nastavení funkce

Chcete-li ověřování instančního objektu služby pro prostředí Azure Stack, definujte koncové body pomocí **get_active_directory_settings()**. Tato metoda používá **ARM_Endpoint** proměnné prostředí, které jste nastavili při vytváření vaší proměnné prostředí.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Můžete použít následující ukázek, které najdete v Githubu repositoreis jako odkaz na vytváření řešení pomocí Ruby a rozhraní API služby Azure Stack profilů:

 - [Správa prostředků a skupin prostředků Azure v Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Správa virtuálních počítačů pomocí Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Nasazení SSH povolené virtuální počítač pomocí šablony v Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Ukázka Resource Manageru a skupiny

Ke spuštění ukázky, ujistěte se, že máte nainstalované Ruby. Pokud používáte Visual Studio Code, stáhněte si sadu Ruby SDK jako rozšíření také. 

> [!Note]  
> Úložiště můžete získat ukázce kódu na "[Správa prostředků Azure a skupiny prostředků pomocí Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Naklonujte úložiště.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Instalace závislosti pomocí sady.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Vytvoření instančního objektu Azure pomocí Powershellu a načíst potřebné hodnoty. 

  Pokyny k vytvoření instančního objektu najdete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Potřebné hodnoty jsou:
  - ID tenanta
  - ID klienta
  - Tajný kód klienta
  - ID předplatného
  - Koncový bod Resource Manageru

  Nastavte následující proměnné prostředí pomocí informace, které jste získali z instanční objekt jste vytvořili.

  - Export AZURE_TENANT_ID = {id vašeho tenanta}
  - Export AZURE_CLIENT_ID = {id klienta}
  - Export AZURE_CLIENT_SECRET = {váš tajný klíč klienta}
  - Export AZURE_SUBSCRIPTION_ID = {id předplatného}
  - Export ARM_ENDPOINT = {váš prostředek AzureStack adresa url správce}

  > [!Note]  
  > Ve Windows použijte sadu místo export.

4. Zkontrolujte, že umístění proměnná je nastavená na vaše AzureStack umístění. Například místní = "local"

5. Přidejte následující řádek kódu, pokud používáte Azure Stack nebo jiných privátních cloudů k cílení koncové body přímo active directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. V rámci možnosti proměnné přidejte nastavení služby active directory a základní adresu URL pro práci s Azure Stack. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Vytvoření profilu klienta, který cílí na profil Azure Stack:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. K ověření objektu služby Azure Stack, musí být definován koncové body pomocí **get_active_directory_settings()**. Tato metoda používá **ARM_Endpoint** proměnné prostředí, které jste nastavili při vytváření vaší proměnné prostředí.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. Spusťte ukázku.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
