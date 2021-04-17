---
title: Vytvoření clusteru Service Fabric pomocí šablony Azure Resource Manager
description: V tomto rychlém startu vytvoříte cluster Azure Service Fabric test pomocí šablony Azure Resource Manager.
author: erikadoyle
ms.author: edoyle
ms.date: 07/29/2020
ms.topic: quickstart
ms.service: service-fabric
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: cb3758ae837dc03789371d00f768b257a9ef295d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535305"
---
# <a name="quickstart-create-a-service-fabric-cluster-using-arm-template"></a>Rychlý Start: Vytvoření clusteru Service Fabric pomocí šablony ARM

Azure Service Fabric je platforma distribuovaných systémů usnadňující balení, nasazování a spravování škálovatelných a spolehlivých mikroslužeb a kontejnerů. *Cluster* Service Fabric je sada virtuálních počítačů připojených k síti, do kterých se vaše mikroslužby nasazují a spravují. Tento článek popisuje, jak nasadit testovací Cluster Service Fabric v Azure pomocí šablony Azure Resource Manager (šablony ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Tento cluster Windows s pěti uzly je zabezpečený pomocí certifikátu podepsaného svým držitelem, který je proto určený jenom pro instruktážní účely (nikoli pro produkční úlohy). K nasazení šablony použijeme Azure PowerShell. Kromě Azure PowerShell můžete použít také Azure Portal, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-portal.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fservice-fabric-secure-cluster-5-node-1-nodetype%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

### <a name="install-service-fabric-sdk-and-powershell-modules"></a>Instalace Service Fabric SDK a modulů PowerShellu

K dokončení tohoto rychlého startu budete potřebovat:

* Nainstalujte [sadu Service Fabric SDK a modul prostředí PowerShell](service-fabric-get-started.md).

* Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="download-the-sample-template-and-certificate-helper-script"></a>Stažení ukázkové šablony a skriptu pomocníka s certifikátem

Naklonujte nebo Stáhněte úložiště [šablon pro rychlý start Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates) . Další možností je zkopírovat místně následující soubory, které budeme používat ze složky *Service-Fabric-Secure-cluster-5-Node-1-NodeType* :

* [New-ServiceFabricClusterCertificate.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/scripts/New-ServiceFabricClusterCertificate.ps1)
* [azuredeploy.jsna](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json)
* [azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.parameters.json)

### <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a určete předplatné, které se má použít k vytvoření clusteru Service Fabric.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

### <a name="create-a-self-signed-certificate-stored-in-key-vault"></a>Vytvoření certifikátu podepsaného svým držitelem uloženého v Key Vault

Service Fabric pomocí certifikátů X. 509 [zabezpečuje cluster](./service-fabric-cluster-security.md) a poskytuje funkce zabezpečení aplikací a [Key Vault](../key-vault/general/overview.md) ke správě těchto certifikátů. Úspěšné vytvoření clusteru vyžaduje certifikát clusteru pro povolení komunikace mezi uzly. Pro účely vytvoření tohoto testovacího clusteru pro rychlý Start vytvoříme certifikát podepsaný svým držitelem pro ověření clusteru. Produkční úlohy vyžadují certifikáty vytvořené pomocí správně nakonfigurované služby Certificate Service systému Windows Server nebo některého od schválené certifikační autority (CA).

```powershell
# Designate unique (within cloudapp.azure.com) names for your resources
$resourceGroupName = "SFQuickstartRG"
$keyVaultName = "SFQuickstartKV"

# Create a new resource group for your Key Vault and Service Fabric cluster
New-AzResourceGroup -Name $resourceGroupName -Location SouthCentralUS

# Create a Key Vault enabled for deployment
New-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $resourceGroupName -Location SouthCentralUS -EnabledForDeployment

# Generate a certificate and upload it to Key Vault
.\scripts\New-ServiceFabricClusterCertificate.ps1
```

Skript vás vyzve k zadání následujících kroků (nezapomeňte upravit *CertDNSName* a *trezor* klíčů z výše uvedených ukázkových hodnot):

* **Heslo:** Heslo! 1
* **CertDNSName:** *sfquickstart*. southcentralus.cloudapp.Azure.com
* **Trezor klíčů:** *SFQuickstartKV*
* **KeyVaultSecretName:** clustercert

Po dokončení bude skript poskytovat hodnoty parametrů potřebné pro nasazení šablony. Nezapomeňte si je Uložit do následujících proměnných, protože budou potřeba k nasazení šablony clusteru:

```powershell
$sourceVaultId = "<Source Vault Resource Id>"
$certUrlValue = "<Certificate URL>"
$certThumbprint = "<Certificate Thumbprint>"
```

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/). Šablona pro tento článek je moc dlouhá, takže se tady nedá zobrazit. Chcete-li zobrazit šablonu, přečtěte si [azuredeploy.jsv](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/service-fabric-secure-cluster-5-node-1-nodetype/azuredeploy.json) souboru.

V šabloně bylo definováno více prostředků Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)
* [Microsoft.Compute/virtualMachineScaleSets](/azure/templates/microsoft.compute/virtualmachinescalesets)
* [Microsoft. ServiceFabric/clustery](/azure/templates/microsoft.servicefabric/clusters)

Další šablony, které souvisejí s Azure Service Fabric, najdete v tématu [šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?sort=Popular&term=service+fabric).

### <a name="customize-the-parameters-file"></a>Přizpůsobení souboru parametrů

Otevřete *azuredeploy.parameters.js* a upravte hodnoty parametrů tak, aby:

* název **clusteru** odpovídá hodnotě, kterou jste zadali pro *CertDNSName* při vytváření certifikátu clusteru.
* **adminUserName** je jiná hodnota než výchozí *gen – jedinečný* token.
* hodnota **adminPassword** je jinou hodnotou než výchozí token *pro obecné heslo* .
* **certificateThumbprint**, **sourceVaultResourceId** a **certificateUrlValue** jsou všechny prázdné řetězce ( `""` ).

Například:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "clusterName": {
      "value": "sfquickstart"
    },
    "adminUsername": {
      "value": "testadm"
    },
    "adminPassword": {
      "value": "Password#1234"
    },
    "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultResourceId": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    }
  }
}
```

## <a name="deploy-the-template"></a>Nasazení šablony

Uložte cesty vaší šablony ARM a souborů parametrů do proměnných a pak šablonu nasaďte.

```powershell
$templateFilePath = "<full path to azuredeploy.json>"
$parameterFilePath = "<full path to azuredeploy.parameters.json>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $certThumbprint `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultResourceId $sourceVaultId `
    -Verbose
```

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Až se nasazení dokončí, vyhledejte `managementEndpoint` hodnotu ve výstupu a otevřete ji ve webovém prohlížeči a zobrazte svůj cluster v [Service Fabric Explorer](./service-fabric-visualizing-your-cluster.md).

![Service Fabric Explorer zobrazení nového clusteru](./media/quickstart-cluster-template/service-fabric-explorer.png)

Koncový bod Service Fabric Explorer můžete najít také v okně svého prostředku v Průzkumníkovi služby v Azure Portal.

![Okno Service Fabric prostředků zobrazující Service Fabric Explorer koncový bod](./media/quickstart-cluster-template/service-fabric-explorer-endpoint-azure-portal.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

```powershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

Dále odeberte certifikát clusteru z místního úložiště. Výpis nainstalovaných certifikátů pro vyhledání kryptografického otisku pro váš cluster:

```powershell
Get-ChildItem Cert:\CurrentUser\My\
```

Pak certifikát odeberte:

```powershell
Get-ChildItem Cert:\CurrentUser\My\{THUMBPRINT} | Remove-Item
```

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření vlastní šablony clusteru Azure Service Fabric najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Vytvoření Správce prostředků šablony Service Fabric clusteru](service-fabric-cluster-creation-create-template.md)
