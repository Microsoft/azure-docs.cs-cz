---
title: Pomocí profilů rozhraní API verze Ruby v zásobníku Azure | Microsoft Docs
description: Další informace o použití profilů verze rozhraní API s Ruby v zásobníku Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 98d285bf8ec92e7b6baf709d4796ce2f4420c4f2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Použití profilů verze rozhraní API s Ruby v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

## <a name="ruby-and-api-version-profiles"></a>Ruby a rozhraní API verze profilů

Ruby SDK pro Azure Resource Manager zásobníku poskytuje nástroje, které vám pomohou vytvářet a spravovat infrastrukturu. Zprostředkovatelé prostředků v sadě SDK zahrnují výpočty, virtuální sítě a úložiště s Ruby jazyk. Profily rozhraní API v sadě SDK Ruby povolit hybridní cloudové vývoj vám pomáhá přepínat mezi globální prostředky Azure a prostředky v Azure zásobníku.

Profil aplikace API je kombinací zprostředkovatelé prostředků a verze aktualizace service. Profil aplikace API můžete kombinovat různé typy prostředků.

 - Chcete-li použít nejnovější verze všech služeb, použijte **nejnovější** profil gem kumulativní Azure SDK.
 - Pokud chcete použít služby kompatibilní s zásobník Azure, použijte **V2017_03_09** profil gem kumulativní Azure SDK.
 - Pokud chcete použít nejnovější verzi rozhraní api služby, použijte **nejnovější** profil konkrétní gem. Například pokud chcete použít nejnovější verzi rozhraní api služby výpočetní samostatně, použijte **nejnovější** profilu **výpočetní** gem.
 - Chcete-li použít konkrétní verzi rozhraní api pro službu, použijte konkrétních verzí rozhraní API definované uvnitř gem.

> [!note] 
> Můžete sloučit všechny možností ve stejné aplikaci.

## <a name="install-the-azure-ruby-sdk"></a>Nainstalujte si Azure SDK Ruby

 - Oficiální podle pokynů k instalaci [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
 - Oficiální podle pokynů k instalaci [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
    - Při instalaci zvolte **přidat Ruby do proměnné PATH**
    - Nainstalujte Dev kit během Ruby instalace po zobrazení výzvy.
    - Dále nainstalujte instalující další produkty pomocí následujícího příkazu:  
      `Gem install bundler`
 - Pokud není k dispozici, vytvořit odběr a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru jsou [zde](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
 - Vytvořit objekt služby a uložte jeho ID a tajný klíč. Pokyny k vytvoření objektu služby pro Azure zásobníku jsou [zde](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals). 
 - Ujistěte se, zda že má vaše objektu služby roli Přispěvatel nebo vlastníka vaše předplatné. Pokyny o tom, jak přiřadit role instanční objekt jsou [zde](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

## <a name="install-the-rubygem-packages"></a>Instalovat balíčky rubygem

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

Mějte na paměti Azure Resource Manager Ruby SDK je ve verzi preview a pravděpodobně bude mít nejnovější změny rozhraní v budoucích verzích. Vyšší číslo podverze může znamenat nejnovější změny.

## <a name="usage-of-the-azuresdk-gem"></a>Využití azure_sdk gem

Gem, azure_sdk, je souhrn všech podporovaných gems v sadě SDK Ruby. Se skládá z této gem **nejnovější** profil, který podporuje nejnovější verzi všech služeb. Zavádí profil verzí **V2017_03_09** profil, který je vytvořen pro Azure zásobníku.

Gem kumulativní azure_sdk můžete nainstalovat pomocí následujícího příkazu:  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>Požadavek

Abyste mohli používat Ruby Azure SDK zásobníkem Azure, musíte zadat následující hodnoty a nastavte hodnoty proměnné prostředí. Postupujte podle pokynů pod tabulkou pro operační systém na nastavení proměnné prostředí. 

| Hodnota | Proměnné prostředí | Popis | 
| --- | --- | --- | --- |
| ID tenanta | AZURE_TENANT_ID | Hodnota zásobník Azure [ID klienta](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview). |
| ID klienta | AZURE_CLIENT_ID | Služba ID hlavní aplikace se neuloží, když instanční objekt byl vytvořen v předchozí části tohoto dokumentu.  |
| ID předplatného | AZURE_SUBSCRIPTION_ID | [ID předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) je, jak přistupovat k nabídky v Azure zásobníku. |
| Tajný klíč klienta | AZURE_CLIENT_SECRET | Aplikace služby hlavní tajný klíč uložit při vytvoření objektu služby. |
| Koncový bod služby Resource Manager | ARM_ENDPOINT | V tématu [zásobníku Azure resource manager endpoin](#The-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod služby zásobník Azure resource manager

Microsoft Azure Resource Manager je rozhraní správy, které umožňuje správcům nasadit, spravovat a sledovat prostředků Azure. Azure Resource Manager může zpracovávat tyto úlohy, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manager můžete získat informace metadat. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.
  > [!note]  
  > **ResourceManagerUrl** je v Azure zásobníku Development Kit (ASDK): `https://management.local.azurestack.external/`  
  > **ResourceManagerUrl** v integrované systémy: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > Pro načtení metadat vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
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

### <a name="set-environmental-variables"></a>Proměnné prostředí sady

**Microsoft Windows**  
Pokud chcete nastavit proměnné prostředí, v příkazovém řádku Windows, použijte následující formát:  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**systému macOS, Linux a Unix systémy**  
V systémech Unix na základě můžete například použít příkaz:  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

Gem kumulativní azure_sdk má následující dva profily:

1. **V2017_03_09**  
  Profil vytvořené pro Azure zásobníku. Pomocí tohoto profilu pro služby být nejvíce kompatibilní s Azure zásobníku.
2. **Nejnovější**  
  Profil se skládá z nejnovější verze všech služeb. Pomocí nejnovější verze všech služeb.

Další informace o profilech zásobník Azure a rozhraní API najdete v tématu [profily souhrn rozhraní API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Použití profilu Azure Ruby rozhraní API sady SDK

Následující řádky se má použít k vytvoření instance profil klienta. Tento parametr je pouze požadované pro zásobník Azure nebo jiné privátní cloudy. Globální Azure již má tato nastavení ve výchozím nastavení.

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

Profil klienta slouží pro přístup k zprostředkovatelé jednotlivých prostředků, jako jsou výpočty, úložiště a sítě.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>Definování funkcí, nastavení AzureStack prostředí

Chcete-li ověřit instanční objekt v prostředí Azure zásobníku, definovat koncové body pomocí **get_active_directory_settings()**. Tato metoda používá **ARM_Endpoint** proměnné prostředí, která jste nastavili při vytváření proměnných prostředí.

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

## <a name="samples-using-api-profiles"></a>Ukázky pomocí profilů rozhraní API

Můžete použít následující ukázky najít v Githubu repositoreis jako odkaz vytváření řešení s profily Ruby a rozhraní API služby Azure zásobníku:

 - [Správa prostředků a skupin prostředků Azure v Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Správa virtuálních počítačů pomocí Ruby](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Nasazení SSH povoleno virtuálních počítačů pomocí šablony v Ruby](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>Ukázka Resource Manager a skupiny

Ke spuštění ukázky, ujistěte se, že jste nainstalovali Ruby. Pokud používáte Visual Studio Code, stáhněte sadu SDK Ruby jako také rozšíření. 

> [!note]  
> Můžete získat úložiště pro ukázku v "[prostředky spravovat Azure a skupiny prostředků s Ruby](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)".

1. Naklonujte úložiště.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. Nainstalujte závislosti pomocí sady.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. Vytvořit objekt služby Azure pomocí prostředí PowerShell a načíst hodnoty potřeby. 

  Pokyny k vytvoření objektu služby najdete v tématu [použití Azure PowerShell k vytvoření objektu služby pomocí certifikátu](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals).

  Hodnoty jsou:
  - ID tenanta
  - ID klienta
  - Tajný klíč klienta
  - ID předplatného
  - Koncový bod služby Resource Manager

  Nastavte následující informace, které jste získali z objektu služby jste vytvořili pomocí proměnné prostředí.

  - Export AZURE_TENANT_ID = {vaše id klienta}
  - Export AZURE_CLIENT_ID = {vaše id klienta}
  - Export AZURE_CLIENT_SECRET = {váš tajný klíč klienta}
  - Export AZURE_SUBSCRIPTION_ID = {svoje id předplatného}
  - Export ARM_ENDPOINT = {AzureStack Resource manager adresa url}

  > [!note]  
  > V systému Windows použijte místo exportu sady.

4. Zkontrolujte, zda že umístění proměnná je nastavená na vaše AzureStack umístění. Například místní = "local"

5. Přidat na následujícím řádku kódu, pokud používáte Azure zásobníku nebo jiných privátních cloudů pro koncové body služby právo active directory.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. Uvnitř proměnnou možnosti přidáte nastavení služby active directory a základní adresu URL pro práci s Azure zásobníku. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Vytvořte profil klienta, který cílí profilem zásobník Azure:

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. K ověření objektu služby Azure zásobníkem, by měl být definován koncové body pomocí **get_active_directory_settings()**. Tato metoda používá **ARM_Endpoint** proměnné prostředí, která jste nastavili při vytváření proměnných prostředí.

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
* [Konfigurace prostředí PowerShell Azure zásobník uživatele](azure-stack-powershell-configure-user.md)  
