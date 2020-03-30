---
title: Vytvoření clusteru Azure Service Fabric
description: Zjistěte, jak nastavit zabezpečený cluster Service Fabric v Azure pomocí Správce prostředků Azure.  Cluster můžete vytvořit pomocí výchozí šablony nebo vlastní šablony clusteru.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 8cf14230f3abd37d91f1ec369f597ee594876100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624116"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Vytvoření clusteru Service Fabric pomocí Správce prostředků Azure 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Portál Azure](service-fabric-cluster-creation-via-portal.md)
>
>

[Cluster Azure Service Fabric](service-fabric-deploy-anywhere.md) je síťová sada virtuálních počítačů, do kterých se vaše mikroslužby nasazují a spravují.  Cluster Service Fabric spuštěný v Azure je prostředek Azure a nasadí se pomocí Správce prostředků Azure. Tento článek popisuje, jak nasadit zabezpečené service fabric clusteru v Azure pomocí Správce prostředků. Můžete použít výchozí šablonu clusteru nebo vlastní šablonu.  Pokud ještě nemáte vlastní šablonu, můžete [se naučit, jak ji vytvořit](service-fabric-cluster-creation-create-template.md).

Typ zabezpečení zvoleného k zabezpečení clusteru (tj.: Identita systému Windows, X509 atd.) musí být určen pro počáteční vytvoření clusteru a poté jej nelze změnit. Před nastavením clusteru si přečtěte [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security]. V Azure service fabric používá certifikát x509 k zabezpečení clusteru a jeho koncových bodů, ověřování klientů a šifrování dat. Azure Active Directory se také doporučuje zabezpečit přístup ke koncovým bodům správy. Další informace načtete [načláneknutí azure ad k ověření klientů](service-fabric-cluster-creation-setup-aad.md).

Pokud vytváříte produkční cluster pro spuštění produkčních úloh, doporučujeme nejprve přečíst [kontrolní seznam připravenosti výroby](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky 
V tomto článku použijte powershellserviceové prostředí Service Fabric RM nebo moduly Azure CLI k nasazení clusteru:

* [Azure PowerShell 4.1 a vyšší][azure-powershell]
* [Azure CLI verze 2.0 a vyšší][azure-CLI]

Referenční dokumentaci pro moduly Service Fabric naleznete zde:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [modul az SF CLI](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Před spuštěním některého z příkazů v tomto článku se nejprve přihlaste do Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Vytvoření nového clusteru pomocí systémově generovaného certifikátu podepsaného svým držitelem

Pomocí následujících příkazů vytvořte cluster zabezpečený systémem generovaným certifikátem podepsaným svým držitelem. Tento příkaz nastaví primární certifikát clusteru, který se používá pro zabezpečení clusteru, a k nastavení přístupu správce k provádění operací správy pomocí tohoto certifikátu.  Certifikáty podepsané svým držitelem jsou užitečné pro zabezpečení testovacích clusterů.  Produkční clustery by měly být zabezpečeny certifikátem certifikační autority .

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Použití výchozí šablony clusteru, která je dodávána v modulu.

Pomocí následujícího příkazu můžete rychle vytvořit cluster zadáním minimálních parametrů pomocí výchozí šablony.

Použitá šablona je dostupná na [ukázkách šablon Azure Service Fabric : šablona windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [šablona Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Následující příkaz můžete vytvořit buď Windows nebo Linux clustery, je třeba zadat operační systém odpovídajícím způsobem. Příkazy prostředí PowerShell/CLI také výstup certifikátu v zadané *CertificateOutputFolder*; přesvědčte se však, zda je složka certifikátu již vytvořena. Příkaz přebírá další parametry, jako je například skladová položka virtuálního řezu.

> [!NOTE]
> Následující příkaz PowerShellu funguje jenom s modulem Azure PowerShell. `Az` Chcete-li zkontrolovat aktuální verzi verze prostředí PowerShell azure správce prostředků, spusťte následující příkaz PowerShellu "Get-Module Az". [Chcete-li](/powershell/azure/install-Az-ps) upgradovat verzi prostředí PowerShell azure správce prostředků, postupujte takto. 
>
>

Nasazení clusteru pomocí PowerShellu:

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

Nasazení clusteru pomocí azure cli:

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

Pokud potřebujete vytvořit vlastní šablonu tak, aby vyhovovala vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici na [ukázkách šablon Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Přečtěte si, jak [přizpůsobit šablonu clusteru][customize-your-cluster-template].

Pokud již vlastní šablonu máte, zkontrolujte, zda jsou všechny tři parametry související s certifikátem v šabloně a souboru parametrů pojmenovány následujícím způsobem a hodnoty jsou null:

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

Nasazení clusteru pomocí PowerShellu:

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

Nasazení clusteru pomocí azure cli:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Vytvoření nového clusteru pomocí vlastního certifikátu X.509

Pokud máte certifikát, pomocí kterého chcete cluster zabezpečit, použijte následující příkaz.

Pokud se jedná o certifikát podepsaný certifikační autoritou, který budete nakonec používat i pro jiné účely, doporučujeme zadat samostatnou skupinu prostředků speciálně pro trezor klíčů. Doporučujeme umístit trezor klíčů do vlastní skupiny prostředků. Tato akce umožňuje odebrat skupiny prostředků výpočetních prostředků a úložiště, včetně skupiny prostředků, která obsahuje cluster Service Fabric, bez ztráty klíčů a tajných klíčů. **Skupina prostředků, která obsahuje trezor *klíčů, musí být ve stejné oblasti* jako cluster, který jej používá.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Použijte výchozí pět uzlů, jeden typ uzlu šablony, která je dodávána v modulu
Použitá šablona je dostupná na [ukázkách Azure: Šablona Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Šablona Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Nasazení clusteru pomocí PowerShellu:

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

Nasazení clusteru pomocí azure cli:

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
Pokud potřebujete vytvořit vlastní šablonu tak, aby vyhovovala vašim potřebám, důrazně doporučujeme začít s jednou ze šablon, které jsou k dispozici na [ukázkách šablon Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Přečtěte si, jak [přizpůsobit šablonu clusteru][customize-your-cluster-template].

Pokud již vlastní šablonu máte, zkontrolujte, zda jsou všechny tři parametry související s certifikátem v šabloně a soubor parametrů pojmenovány následujícím způsobem a hodnoty jsou null.

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

Nasazení clusteru pomocí PowerShellu:

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

Nasazení clusteru pomocí azure cli:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Použití ukazatele na tajný klíč nahraný do trezoru klíčů

Chcete-li použít existující trezor klíčů, musí být povolen trezor klíčů [pro nasazení,](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) aby poskytovatel výpočetních prostředků mohl získat certifikáty a nainstalovat jej do uzlů clusteru.

Nasazení clusteru pomocí PowerShellu:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Nasazení clusteru pomocí azure cli:

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
V tomto okamžiku máte v Azure spuštěný zabezpečený cluster. Dále [se připojte ke clusteru a zjistěte,](service-fabric-connect-to-secure-cluster.md) jak [spravovat tajné kódy aplikací](service-fabric-application-secret-management.md).

Syntaxe a vlastnosti JSON pro použití šablony naleznete v [tématu Microsoft.ServiceFabric/clusters reference šablony](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
