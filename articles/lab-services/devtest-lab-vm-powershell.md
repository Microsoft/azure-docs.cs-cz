---
title: Vytvoření virtuálního počítače ve službě DevTest Labs pomocí Azure Powershellu | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Azure DevTest Labs k vytváření a správě virtuálních počítačů pomocí Azure Powershellu.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622435"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Vytvoření virtuálního počítače s DevTest Labs s využitím Azure Powershellu
Tento článek ukazuje, jak vytvořit virtuální počítač ve službě Azure DevTest Labs pomocí Azure Powershellu. Skripty prostředí PowerShell můžete použít k automatizaci vytváření virtuálních počítačů v testovacím prostředí ve službě Azure DevTest Labs. 

## <a name="prerequisites"></a>Požadavky
Než začnete:

- [Vytvoření testovacího prostředí](devtest-lab-create-lab.md) Pokud nechcete použít existující testovací prostředí pro testování skripty nebo příkazy v tomto článku. 
- [Nainstalovat Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) nebo pomocí služby Azure Cloud Shell, který je integrovaný do portálu Azure portal. 

## <a name="powershell-script"></a>Skript PowerShellu
Ukázkový skript v této části se používá [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) rutiny.  Tato rutina použije ID prostředku testovacího prostředí, název akce k provedení (`createEnvironment`), a potřebné parametry provedení této akce. Parametry jsou v zatřiďovací tabulce, která obsahuje všechny vlastnosti Popis virtuálního počítače. 

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

Vlastnosti pro virtuální počítač ve výše uvedené skriptu umožňuje vytvoření virtuálního počítače s Windows Server 2016 DataCenter jako operační systém. Pro každý typ virtuálního počítače tyto vlastnosti budou mírně lišit. [Definovat virtuální počítač](#define-virtual-machine) části se dozvíte, jak určit, které vlastnosti se mají použít v tomto skriptu.

Následující příkaz poskytuje příklad skriptu pro uložení v názvu souboru: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definování virtuálních počítačů
V této části se dozvíte, jak získat vlastnosti, které jsou specifické pro daný typ virtuálního počítače, který chcete vytvořit. 

### <a name="use-azure-portal"></a>Použití webu Azure Portal
Šablony Azure Resource Manageru můžete generovat při vytváření virtuálního počítače na webu Azure Portal. Není nutné pro dokončení procesu vytváření virtuálního počítače. Pouze provedením kroků, dokud se šablony zobrazí. Toto je nejlepší způsob, jak získat potřebné popis JSON, pokud již nemáte testovacího prostředí, které se vytvoří virtuální počítač. 

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v navigační nabídce vlevo.
3. Vyhledejte a vyberte **DevTest Labs** ze seznamu služeb. 
4. Na **DevTest Labs** vyberte vaše testovací prostředí v seznamu testovacích prostředí.
5. Na domovské stránce testovacího prostředí, vyberte **+ přidat** na panelu nástrojů. 
6. Vyberte **základní image** pro virtuální počítač. 
7. Vyberte **Možnosti automatizace** v dolní části stránky výše **odeslat** tlačítko. 
8. Zobrazí **šablony Azure Resource Manageru** při vytváření virtuálního počítače. 
9. Segment do formátu JSON **prostředky** část obsahuje definici pro typ obrázku, který jste vybrali dříve. 

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

V tomto příkladu můžete zjistit, jak získat definici Azure Marketplace image. Stejným způsobem můžete získat definici z vlastní image, vzorce nebo prostředí. Přidat všechny artefakty potřebné pro virtuální počítač a nastavit Pokročilá nastavení vyžaduje. Po zadání hodnot požadovaných polí a volitelných polí, před výběrem **Možnosti automatizace** tlačítko.

### <a name="use-azure-rest-api"></a>Použití rozhraní Azure REST API
Následující postup obsahuje kroky k získání vlastnosti image pomocí rozhraní REST API: Tyto kroky fungují pouze u existujícího virtuálního počítače v testovacím prostředí. 

1. Přejděte [seznamu virtuálních počítačů –](/rest/api/dtl/virtualmachines/list) stránce **vyzkoušet** tlačítko. 
2. Vyberte své **předplatné Azure**.
3. Zadejte **skupiny prostředků pro testovací prostředí**.
4. Zadejte **název testovacího prostředí**. 
5. Vyberte **Run** (Spustit).
6. Zobrazí **vlastností pro bitovou kopii** založené na virtuální počítač vytvořil. 



## <a name="next-steps"></a>Další postup
Viz následující obsah: [Dokumentace k Azure Powershellu pro Azure DevTest Labs](/powershell/module/az.devtestlabs/)
