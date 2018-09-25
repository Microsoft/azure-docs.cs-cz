---
title: Vytvoření clusteru Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zabezpečení clusteru Service Fabric v Azure pomocí Azure Resource Manageru.  Můžete vytvořit cluster pomocí výchozí šablony nebo pomocí šablony clusteru.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: b71c1d75dc9d988c7b26f9720cc03d9fee9109b1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970307"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Vytvoření clusteru Service Fabric pomocí Azure Resource Manageru 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

[Clusteru Azure Service Fabric](service-fabric-deploy-anywhere.md) je síťově propojená sada virtuálních počítačů, do které se nasazují a spravují mikroslužby.  Cluster Service Fabric běžící v Azure je prostředek Azure a nasazení pomocí Azure Resource Manageru. Tento článek popisuje postup nasazení zabezpečeného clusteru Service Fabric v Azure pomocí Resource Manageru. Můžete použít výchozí šablonu clusteru nebo vlastní šablony.  Pokud ještě nemáte vlastní šablonu, můžete si [zjistěte, jak vytvořit](service-fabric-cluster-creation-create-template.md).

Konfigurace zabezpečení clusteru při jeho je první instalace není možné později změnit. Před nastavením clusteru, přečtěte si [scénáře zabezpečení clusteru Service Fabric][service-fabric-cluster-security]. V Azure Service Fabric používá x509 certifikátů k zabezpečení vašeho clusteru a její koncové body, ověřování klientů a šifrování dat. Azure Active Directory je také vhodné k zabezpečení přístupu ke koncovým bodům správy. Azure AD klienti a uživatelé musí být vytvořen před vytvořením clusteru.  Další informace najdete v článku [nastavení Azure AD pro ověřování klientů](service-fabric-cluster-creation-setup-aad.md).

Pokud vytváříte cluster pro produkční prostředí ke spuštění úlohy v produkčním prostředí, doporučujeme vám první čtení až [připravenosti produkční kontrolní seznam](service-fabric-production-readiness-checklist.md).

## <a name="prerequisites"></a>Požadavky 
V tomto článku se použijte k nasazení clusteru Service Fabric RM powershellu nebo rozhraní příkazového řádku Azure moduly:

* [Prostředí Azure PowerShell 4.1 a vyšší][azure-powershell]
* [Azure CLI verze 2.0 a vyšší][azure-CLI]

Referenční dokumentace pro Service Fabric moduly tady nenajdete:
* [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric)
* [modul az SF rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Než spustíte všechny příkazy v tomto článku, nejdřív přihlaste k Azure.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Vytvořit nový cluster využívající systém vygeneruje certifikát podepsaný svým držitelem

Pomocí následujících příkazů vytvořit cluster zabezpečený pomocí certifikátu podepsaného svým držitelem generována. Tento příkaz nastaví primární clusteru certifikát, který se používá pro zabezpečení clusteru a jak nastavit přístup správce k provádění operací správy pomocí certifikátu.  Certifikáty podepsané svým držitelem jsou užitečné k zabezpečení testovacích clusterů.  Produkční clustery by měl být zabezpečený pomocí certifikátu od certifikační autority (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Použít výchozí šablonu clusteru, který se dodává v modulu

Použijte následující příkaz rychle vytvořit cluster tak, že zadáte minimálního počtu parametrů, pomocí výchozí šablony.

Šablonu, která se použije je k dispozici na [ukázkové šablony Azure Service Fabric: Šablona windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Následujícího příkazu můžete vytvořit buď Windows nebo Linux clusterů, je třeba zadat operačního systému odpovídajícím způsobem. Příkazy prostředí PowerShell nebo rozhraní příkazového řádku také výstupní certifikát v zadaném *CertificateOutputFolder*; nicméně, ujistěte se, že složka certifikát už vytvořili. Příkaz přijímá další parametry například skladovou Položku virtuálního počítače stejně.

> [!NOTE]
> Následující příkaz Powershellu funguje jenom s Azure Resource Manageru s prostředím PowerShell verze > 6.1. Zkontrolujte aktuální verzi Azure Powershellu pro Resource Manager verze, spusťte následující příkaz prostředí PowerShell "Get-Module AzureRM". Postupujte podle [tento odkaz](/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0) k upgradu verze Azure Powershellu pro Resource Manager. 
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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
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

### <a name="use-your-own-custom-template"></a>Použít vlastní šablonu

Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, důrazně doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Zjistěte, jak [přizpůsobení šablony clusteru][customize-your-cluster-template].

Pokud už máte vlastní šablonu, zkontrolujte, že všechny tři certifikát související s parametry v šabloně a soubor parametrů jsou pojmenovány následujícím způsobem a hodnoty null jsou následující:

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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Vytvoření nového clusteru pomocí certifikátu X.509

Použijte následující příkaz k vytvoření clusteru, pokud máte certifikát, který chcete použít k zabezpečení clusteru pomocí.

Pokud certifikát podepsaný certifikační Autoritou, která skončíte použití pro jiné účely a, pak doporučujeme zadat do jiné skupiny prostředků pro váš trezor klíčů. Doporučujeme umístit služby key vault do své vlastní skupině prostředků. Tato akce umožňuje odebrání výpočetních prostředků a úložiště skupiny prostředků, včetně skupiny prostředků, která obsahuje cluster Service Fabric, aniž by ztratily klíče a tajné kódy. **Skupina prostředků obsahující trezor klíčů *musí být ve stejné oblasti* jako cluster, který je používán.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Použít výchozí pěti uzly, jeden uzel šablony, která se dodává v modulu
Šablonu, která se použije je k dispozici na [ukázky v Azure: Šablona Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) a [Ubuntu šablony](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
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

### <a name="use-your-own-custom-cluster-template"></a>Použít vlastní šablonu vlastního clusteru
Pokud je potřeba vytvořit vlastní šablony tak, aby odpovídala vašim potřebám, důrazně doporučujeme začít s některou ze šablon, které jsou k dispozici na [ukázkové šablony Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Zjistěte, jak [přizpůsobení šablony clusteru][customize-your-cluster-template].

Pokud již jste vlastní šablony, pak se ujistěte se, že Překontrolujte, že všechny tři certifikát související jsou takto pojmenovaných parametrů v šabloně a soubor parametrů a hodnot mají hodnotu null následujícím způsobem.

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
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Použije ukazatel na tajný klíč nahraje do služby key vault

Pokud chcete používat existujícího trezoru klíčů, musí být služby key vault [povolen pro nasazení](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) umožňující poskytovateli prostředků compute k získání certifikátů z něj a nainstalujte ho na uzlech clusteru.

Nasazení clusteru pomocí prostředí PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Nasazení clusteru pomocí rozhraní příkazového řádku Azure:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Další postup
V tomto okamžiku máte zabezpečeného clusteru v Azure. Dále [připojení k vašemu clusteru](service-fabric-connect-to-secure-cluster.md) a zjistěte, jak [Správa tajných klíčů aplikací](service-fabric-application-secret-management.md).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template
