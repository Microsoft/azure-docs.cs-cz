---
title: Vytvoření virtuálního počítače v DevTest Labs pomocí Azure PowerShell
description: Naučte se používat Azure DevTest Labs k vytváření a správě virtuálních počítačů s Azure PowerShell.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499250"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Vytvoření virtuálního počítače s DevTest Labs pomocí Azure PowerShell
V tomto článku se dozvíte, jak vytvořit virtuální počítač v Azure DevTest Labs pomocí Azure PowerShell. PowerShellové skripty můžete použít k automatizaci vytváření virtuálních počítačů v testovacím prostředí v Azure DevTest Labs. 

## <a name="prerequisites"></a>Požadavky
Než začnete:

- [Vytvořte testovací prostředí](devtest-lab-create-lab.md) , pokud nechcete použít existující testovací prostředí k testování skriptu nebo příkazů v tomto článku. 
- [Nainstalujte Azure PowerShell](/powershell/azure/install-az-ps) nebo použijte Azure Cloud Shell integrovaný do Azure Portal. 

## <a name="powershell-script"></a>Skript prostředí PowerShell
Vzorový skript v této části používá rutinu [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) .  Tato rutina převezme ID prostředku testovacího prostředí, název akce k provedení ( `createEnvironment` ) a parametry potřebné k provedení této akce. Parametry jsou v zatřiďovací tabulce, která obsahuje všechny vlastnosti popisu virtuálního počítače. 

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

Vlastnosti virtuálního počítače ve výše uvedeném skriptu nám umožňují vytvořit virtuální počítač s Windows serverem 2016 DataCenter jako operačním systémem. U každého typu virtuálního počítače se tyto vlastnosti mírně liší. V části [definice virtuálního počítače](#define-virtual-machine) se dozvíte, jak určit vlastnosti, které se mají použít v tomto skriptu.

Následující příkaz uvádí příklad spuštění skriptu uloženého v názvu souboru: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definovat virtuální počítač
V této části se dozvíte, jak získat vlastnosti, které jsou specifické pro typ virtuálního počítače, který chcete vytvořit. 

### <a name="use-azure-portal"></a>Použití webu Azure Portal
Při vytváření virtuálního počítače v Azure Portal můžete vygenerovat šablonu Azure Resource Manager. Nemusíte dokončit proces vytváření virtuálního počítače. Postup bude provedete pouze v případě, že jste šablonu viděli. Toto je nejlepší způsob, jak získat potřebný popis JSON, pokud ještě nemáte vytvořený virtuální počítač testovacího prostředí. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V levé navigační nabídce vyberte **všechny služby** .
3. Vyhledejte a v seznamu služeb vyberte **DevTest Labs** . 
4. Na stránce **DevTest Labs** vyberte testovací prostředí v seznamu cvičení.
5. Na domovské stránce testovacího prostředí vyberte **+ Přidat** na panelu nástrojů. 
6. Vyberte **základní image** pro virtuální počítač. 
7. V dolní části stránky nad tlačítkem **Odeslat** vyberte **Možnosti automatizace** . 
8. Zobrazí se **šablona Azure Resource Manager** pro vytvoření virtuálního počítače. 
9. Segment JSON v části **Resources (prostředky** ) obsahuje definici pro typ obrázku, který jste vybrali dříve. 

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

V tomto příkladu vidíte, jak získat definici obrázku na místě na trhu Azure. Stejným způsobem můžete získat definici vlastní image, vzorce nebo prostředí. Přidejte všechny artefakty potřebné pro virtuální počítač a nastavte všechna požadovaná upřesňující nastavení. Po zadání hodnot požadovaných polí a všech volitelných polí před výběrem tlačítka **Možnosti automatizace** .

### <a name="use-azure-rest-api"></a>Použití Azure REST API
Následující postup obsahuje kroky pro získání vlastností obrázku pomocí REST API: tyto kroky fungují pouze pro existující virtuální počítač v testovacím prostředí. 

1. Přejděte na stránku [Virtual Machines – seznam](/rest/api/dtl/virtualmachines/list) , vyberte tlačítko **vyzkoušet** . 
2. Vyberte své **předplatné Azure**.
3. Zadejte **skupinu prostředků pro testovací prostředí**.
4. Zadejte **název testovacího prostředí**. 
5. Vyberte **Run** (Spustit).
6. Zobrazí se **Vlastnosti bitové kopie** , na základě které byl virtuální počítač vytvořen. 

## <a name="set-expiration-date"></a>Nastavit datum vypršení platnosti
Ve scénářích, jako jsou například školení, ukázky a zkušební verze, můžete chtít vytvořit virtuální počítače a odstranit je automaticky po pevné době trvání, takže nebudete mít zbytečné náklady. Můžete nastavit datum vypršení platnosti virtuálního počítače při jeho vytváření pomocí prostředí PowerShell, jak je znázorněno v části ukázkový [skript prostředí PowerShell](#powershell-script) .

Tady je ukázkový skript PowerShellu, který nastaví datum vypršení platnosti pro všechny existující virtuální počítače v testovacím prostředí:

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
Podívejte se na následující obsah: [Azure PowerShell dokumentaci pro Azure DevTest Labs](/powershell/module/az.devtestlabs/)
