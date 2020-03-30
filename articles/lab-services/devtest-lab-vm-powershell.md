---
title: Vytvoření virtuálního počítače v devtest labs s Azure PowerShellem
description: Zjistěte, jak pomocí Azure DevTest Labs vytvářet a spravovat virtuální počítače pomocí Azure PowerShellu.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167116"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Vytvoření virtuálního počítače pomocí devTest Labs pomocí Azure PowerShellu
Tento článek ukazuje, jak vytvořit virtuální počítač v Azure DevTest Labs pomocí Azure PowerShellu. Pomocí skriptů prostředí PowerShell můžete automatizovat vytváření virtuálních počítačů v testovacím prostředí v azure devtest labs. 

## <a name="prerequisites"></a>Požadavky
Než začnete:

- Pokud nechcete k testování skriptu nebo příkazů v tomto článku použít existující testovací prostředí, vytvořte [testovací prostředí.](devtest-lab-create-lab.md) 
- [Nainstalujte Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) nebo použijte Azure Cloud Shell, který je integrovaný na webu Azure Portal. 

## <a name="powershell-script"></a>Skript PowerShellu
Ukázkový skript v této části používá rutinu [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Tato rutina přebírá ID prostředku testovacího prostředí, název`createEnvironment`akce, která má být vykonána ( ), a parametry nezbytné provedení této akce. Parametry jsou v tabulce hash, která obsahuje všechny vlastnosti popisu virtuálního počítače. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Vlastnosti virtuálního počítače ve výše uvedeném skriptu nám umožňují vytvořit virtuální počítač s Windows Server 2016 DataCenter jako operační systém. Pro každý typ virtuálního počítače se tyto vlastnosti mírně liší. V části [Definovat virtuální počítač](#define-virtual-machine) se zobrazí, jak určit, které vlastnosti se mají v tomto skriptu použít.

Následující příkaz poskytuje příklad spuštění skriptu uloženého v názvu souboru: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definování virtuálního počítače
V této části se zobrazí, jak získat vlastnosti, které jsou specifické pro typ virtuálního počítače, který chcete vytvořit. 

### <a name="use-azure-portal"></a>Použití webu Azure Portal
Šablonu Azure Resource Manageru můžete vygenerovat při vytváření virtuálního počítače na webu Azure Portal. Není nutné dokončit proces vytváření virtuálního virtuálního mísy. Postupujte pouze podle pokynů, dokud se nezobrazí šablona. Toto je nejlepší způsob, jak získat potřebný popis JSON, pokud ještě nemáte vytvořený virtuální počítač testovacího prostředí. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V levé navigační nabídce vyberte **Všechny služby.**
3. Vyhledejte a vyberte **DevTest Labs** ze seznamu služeb. 
4. Na stránce **DevTest Labs** vyberte testovací prostředí v seznamu testovacích prostředí.
5. Na domovské stránce testovacího prostředí vyberte **+ Přidat** na panelu nástrojů. 
6. Vyberte **základní image** pro virtuální hod. 
7. Vdolní části stránky nad tlačítkem **Odeslat** vyberte **možnosti automatizace.** 
8. Zobrazí se **šablona Azure Resource Manager** pro vytvoření virtuálního počítače. 
9. Segment JSON v části **zdroje** má definici pro typ obrázku, který jste vybrali dříve. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

V tomto příkladu uvidíte, jak získat definici image Azure Market Place. Stejným způsobem můžete získat definici vlastního obrázku, vzorce nebo prostředí. Přidejte všechny artefakty potřebné pro virtuální počítač a nastavte všechna požadovaná pokročilá nastavení. Po zadání hodnot pro požadovaná pole a všechna volitelná pole před výběrem tlačítka **Možnosti automatizace.**

### <a name="use-azure-rest-api"></a>Použití rozhraní AZURE REST API
Následující postup poskytuje kroky k získání vlastností bitové kopie pomocí rozhraní REST API: Tyto kroky fungují pouze pro existující virtuální počítače v testovacím prostředí. 

1. Přejděte na stránku [se seznamem Virtuální počítače –](/rest/api/dtl/virtualmachines/list) vyberte tlačítko **Vyzkoušet.** 
2. Vyberte své **předplatné Azure**.
3. Zadejte **skupinu prostředků pro testovací prostředí**.
4. Zadejte **název testovacího prostředí**. 
5. Vyberte **Run** (Spustit).
6. Zobrazí se **vlastnosti pro image** na základě, na kterém byl vytvořen virtuální počítač. 

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Ve scénářích, jako je školení, ukázky a zkušební verze, můžete chtít vytvořit virtuální počítače a odstranit je automaticky po pevné době trvání, takže vám nevzniknou zbytečné náklady. Můžete nastavit datum vypršení platnosti pro virtuální ho při vytváření pomocí PowerShellu, jak je znázorněno v příkladu skriptu [PowerShellu](#powershell-script) části.

Tady je ukázkový skript prostředí PowerShell, který určuje datum vypršení platnosti pro všechny existující virtuální hody v testovacím prostředí:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Další kroky
Podívejte se na následující obsah: [Dokumentace k Azure PowerShellu pro Azure DevTest Labs](/powershell/module/az.devtestlabs/)
