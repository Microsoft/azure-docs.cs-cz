---
title: Vytvoření clusteru Azure Service Fabric
description: Přečtěte si, jak nastavit zabezpečený Service Fabric cluster v Azure pomocí Azure Resource Manager.  Cluster můžete vytvořit pomocí výchozí šablony nebo pomocí vlastní šablony clusteru.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: d4daa75fd9383bc19da1b09104ebddff5712c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791807"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Vytvoření clusteru Service Fabric pomocí Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) je sada virtuálních počítačů připojených k síti, do kterých se nasazují a spravují vaše mikroslužby.  Service Fabric cluster se systémem v Azure je prostředek Azure, který je nasazený pomocí Azure Resource Manager. Tento článek popisuje, jak nasadit zabezpečený Service Fabric cluster v Azure pomocí Správce prostředků. Můžete použít výchozí šablonu clusteru nebo vlastní šablonu.  Pokud ještě nemáte vlastní šablonu, můžete se [dozvědět, jak si ji vytvořit](service-fabric-cluster-creation-create-template.md).

Typ zabezpečení zvoleného pro zabezpečení clusteru (tj.: identita systému Windows, identifikátor x509 atd.) je nutné zadat pro počáteční vytvoření clusteru a poté jej nelze změnit. Před nastavením clusteru si přečtěte [Service Fabric scénáře zabezpečení clusteru][service-fabric-cluster-security]. V Azure Service Fabric používá certifikát x509 k zabezpečení clusteru a jeho koncových bodů, ověřování klientů a šifrování dat. Azure Active Directory také doporučujeme zabezpečit přístup ke koncovým bodům správy. Pokud potřebujete další informace, přečtěte si téma [Nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

Pokud vytváříte provozní cluster pro spouštění produkčních úloh, doporučujeme si nejdřív projít [Kontrolní seznam připravenosti na produkci](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky 
V tomto článku nasadíte cluster pomocí modulu Service Fabric RM PowerShell nebo Azure CLI:

* [Azure PowerShell 4,1 a vyšší][azure-powershell]
* [Azure CLI verze 2,0 a vyšší][azure-CLI]

Referenční dokumentaci k modulům Service Fabric najdete tady:
* [Az.ServiceFabric](/powershell/module/az.servicefabric)
* [AZ SF CLI Module](/cli/azure/sf)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Před spuštěním kteréhokoli z příkazů v tomto článku se nejdřív přihlaste k Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Vytvoření nového clusteru pomocí systému generovaného certifikátem podepsaným svým držitelem

Pomocí následujících příkazů vytvořte cluster zabezpečený systémem generovaným certifikátem podepsaným svým držitelem. Tento příkaz nastaví primární clusterový certifikát, který se používá k zabezpečení clusteru, a k nastavení přístupu správce k provádění operací správy pomocí tohoto certifikátu.  Certifikáty podepsané svým držitelem jsou užitečné pro zabezpečení testovacích clusterů.  Provozní clustery by se měly zabezpečit pomocí certifikátu od certifikační autority (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Použít výchozí šablonu clusteru, která je dodávána v modulu

Pomocí následujícího příkazu můžete rychle vytvořit cluster zadáním minimálních parametrů pomocí výchozí šablony.

Použitá šablona je k dispozici v [ukázkách šablon Azure Service Fabric: Šablona Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Následující příkaz může vytvořit clustery se systémem Windows nebo Linux, a proto je nutné zadat odpovídající operační systém. Příkazy PowerShellu/CLI také výstupují certifikát v zadaném *CertificateOutputFolder*; Ujistěte se však, že je složka s certifikátem již vytvořena. Příkaz používá i jiné parametry, jako je třeba SKU virtuálního počítače.

> [!NOTE]
> Následující příkaz PowerShell funguje jenom s `Az` modulem Azure PowerShell. Pokud chcete zjistit aktuální verzi Azure Resource Manager PowerShellu, spusťte následující příkaz PowerShellu Get-Module AZ. Pomocí [tohoto odkazu](/powershell/azure/install-Az-ps) můžete upgradovat Azure Resource Manager powershellovou verzi. 
>
>

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Použití vlastní šablony

Pokud potřebujete vytvořit vlastní šablonu, která bude vyhovovat vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici v [ukázkách šablon Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Přečtěte si, jak [přizpůsobit šablonu clusteru][customize-your-cluster-template].

Pokud již máte vlastní šablonu, překontrolujte, že všechny tři parametry související s certifikátem v šabloně a souboru parametrů jsou pojmenovány takto a hodnoty jsou null následujícím způsobem:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Vytvoření nového clusteru pomocí vlastního certifikátu X. 509

Pomocí následujícího příkazu vytvořte cluster, pokud máte certifikát, pomocí kterého chcete zabezpečit svůj cluster.

Pokud se jedná o certifikát podepsaný certifikační autoritou, který budete mít k dispozici i pro jiné účely, doporučuje se zadat odlišnou skupinu prostředků specifickou pro váš Trezor klíčů. Doporučujeme vložit Trezor klíčů do vlastní skupiny prostředků. Tato akce vám umožní odebrat skupiny prostředků COMPUTE a Storage, včetně skupiny prostředků, která obsahuje váš Cluster Service Fabric, aniž by došlo ke ztrátě klíčů a tajných kódů. **Skupina prostředků, která obsahuje váš Trezor klíčů, *musí být ve stejné oblasti* jako cluster, který ho používá.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Použijte výchozí pět uzlů, jednu šablonu typu uzlu, která je dodávána v modulu.
Použitá šablona je dostupná na [ukázkách Azure: Šablona Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Použití vlastní šablony clusteru
Pokud potřebujete vytvořit vlastní šablonu, která bude vyhovovat vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici v [ukázkách šablon Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Přečtěte si, jak [přizpůsobit šablonu clusteru][customize-your-cluster-template].

Pokud již máte vlastní šablonu, ujistěte se, že jsou všechny tři parametry související s certifikátem v šabloně a souborem parametrů pojmenovány takto a hodnoty mají hodnotu null následujícím způsobem.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Použití ukazatele na tajný kód nahraný do trezoru klíčů

Aby bylo možné použít existující Trezor klíčů, musí být [povolený Trezor klíčů pro nasazení](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) , aby poskytovateli výpočetních prostředků mohl získat certifikáty z něj a nainstalovat ho na uzly clusteru.

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Další kroky
V tomto okamžiku máte zabezpečený cluster běžící v Azure. Pak se [připojte ke svému clusteru](service-fabric-connect-to-secure-cluster.md) a Naučte se [spravovat tajné klíče aplikace](service-fabric-application-secret-management.md).

Informace o syntaxi a vlastnostech JSON pro použití šablony naleznete v tématu [Reference k šabloně Microsoft. ServiceFabric/Clusters](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
