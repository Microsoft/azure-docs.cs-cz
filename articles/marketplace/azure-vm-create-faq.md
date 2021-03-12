---
title: Běžné dotazy k virtuálnímu počítači v Azure Marketplace
description: Při vytváření virtuálního počítače v Azure Marketplace se zjistily běžné otázky.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 03/10/2021
ms.openlocfilehash: a74170af61c05d07a189b5ceb61dc0c9b7e14298
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200428"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Běžné dotazy k virtuálnímu počítači v Azure Marketplace

Tyto nejčastější dotazy se týkají běžných problémů, se kterými se můžete setkat při vytváření nabídky virtuálního počítače v Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Návody nakonfigurovat virtuální privátní síť (VPN) pro práci s vlastními virtuálními počítači?

Pokud používáte model nasazení Azure Resource Manager, máte k dispozici tři možnosti:

- [Vytvoření brány sítě VPN založené na trasách pomocí Azure Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Vytvoření brány sítě VPN založené na trasách pomocí Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Vytvoření brány sítě VPN založené na trasách pomocí rozhraní příkazového řádku](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Jaké jsou zásady podpory Microsoftu pro používání serverového softwaru Microsoftu na virtuálních počítačích založených na Azure?

Podrobnosti najdete na [webu podpora serverového softwaru Microsoftu pro Microsoft Azure virtuálních počítačů](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Jak se ve virtuálním počítači dá spravovat rozšíření vlastních skriptů v úloze po spuštění?

Podrobnosti o použití rozšíření vlastních skriptů pomocí modulu Azure PowerShell, Azure Resource Manager šablon a postupu při řešení potíží v systémech Windows najdete v tématu [rozšíření vlastních skriptů pro Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Jsou 32 aplikace nebo služby podporované v Azure Marketplace?

No. Podporované operační systémy a standardní služby pro virtuální počítače Azure jsou v 64 bitů. I když většina 64 operačních systémů podporuje 32 verzí aplikací pro zpětnou kompatibilitu, použití 32 bitových aplikací jako součást řešení pro virtuální počítače se nepodporuje a důrazně se nedoporučuje. Znovu vytvořte aplikaci jako 64 projekt.

Další informace najdete v těchto článcích:

- [Spouštění 32 aplikací](/windows/desktop/WinProg64/running-32-bit-applications)
- [Podpora 32bitových operačních systémů ve virtuálních počítačích Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Podpora serverového softwaru Microsoft pro virtuální počítače Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Chyba: virtuální pevný disk je už zaregistrovaný s úložištěm imagí jako prostředek

Pokaždé, když se pokusím vytvořit image z mých virtuálních pevných disků, zobrazí se chyba "virtuální pevný disk je už v úložišti imagí zaregistrovaný jako prostředek" v Azure PowerShell. Nevytvořil (a) jsem obrázek, ale v Azure nebyl nalezen žádný obrázek s tímto názvem. Jak to můžu vyřešit?

K tomuto problému obvykle dochází, pokud jste vytvořili virtuální počítač z virtuálního pevného disku, který je na něm zamčený. Potvrďte, že z tohoto virtuálního pevného disku není přidělený žádný virtuální počítač, a potom zkuste operaci zopakovat. Pokud se tento problém opakuje, otevřete lístek podpory. Viz [Podpora partnerského centra](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Návody vytvořit virtuální počítač z zobecněného virtuálního pevného disku?

### <a name="prepare-an-azure-resource-manager-template"></a>Příprava šablony Azure Resource Manager

Tato část popisuje, jak vytvořit a nasadit image virtuálního počítače (VM) zadaného uživatelem. To můžete provést tak, že zadáte image virtuálního pevného disku operačního systému a datového disku z virtuálního pevného disku nasazeného v Azure. Tyto kroky nasadí virtuální počítač pomocí zobecněného virtuálního pevného disku.

1. Přihlaste se k portálu Azure.
2. Nahrajte na účet Azure Storage svůj zobecněný virtuální pevný disk s operačním systémem a virtuální pevné disky s daty.
3. Na domovské stránce vyberte vytvořit prostředek, vyhledejte "nasazení šablony" a vyberte vytvořit.
4. V editoru vyberte vytvořit vlastní šablonu.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Zobrazuje výběr šablony.":::

5. Do editoru vložte následující šablonu JSON a vyberte Uložit.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Zadejte hodnoty parametrů pro zobrazené stránky vlastností vlastního nasazení.

 **TABULKA 1**

| **ResourceGroupName** | **Název existující skupiny prostředků Azure Obvykle používejte stejný RG jako Trezor klíčů.** |
| --- | --- |
| TemplateFile | Úplná cesta k souboru VHDtoImage.jsv. |
| userStorageAccountName | Název účtu úložiště |
| dnsNameForPublicIP | Název DNS pro veřejnou IP adresu; musí být malými písmeny. |
| subscriptionId | Identifikátor předplatného Azure. |
| Umístění | Standardní geografické umístění skupiny prostředků v Azure. |
| vmName | Název virtuálního počítače |
| vhdUrl | Webová adresa virtuálního pevného disku. |
| vmSize | Velikost instance virtuálního počítače |
| publicIPAddressName | Název veřejné IP adresy. |
| virtualNetworkName | Název virtuální sítě. |
| nicName | Název karty síťového rozhraní pro virtuální síť. |
| adminUserName | Uživatelské jméno účtu správce. |
| adminPassword | Heslo správce. |
|

7. Po poskytnutí těchto hodnot vyberte koupit.

Azure spustí nasazení. Vytvoří nový virtuální počítač se zadaným nespravovaným virtuálním pevným diskem v zadané cestě k účtu úložiště. Průběh můžete sledovat v Azure Portal výběrem Virtual Machines na levé straně portálu. Po vytvoření virtuálního počítače se stav změní z počáteční na spuštěno.

Pro nasazení virtuálního počítače generace 2 použijte tuto šablonu:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>Nasazení virtuálního počítače Azure pomocí PowerShellu

Zkopírujte a upravte následující skript, který poskytne hodnoty pro `$storageaccount` proměnné a `$vhdUrl` . Spusťte ho k vytvoření prostředku virtuálního počítače Azure ze stávajícího zobecněného virtuálního pevného disku.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```


## <a name="next-steps"></a>Další kroky

- [Řešení potíží s certifikacemi virtuálních počítačů](azure-vm-create-certification-faq.md)
