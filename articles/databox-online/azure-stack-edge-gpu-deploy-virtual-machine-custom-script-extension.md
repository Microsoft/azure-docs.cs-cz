---
title: Použití rozšíření vlastních skriptů pro virtuální počítače na zařízení Azure Stack Edge pro
description: Popisuje, jak nainstalovat rozšíření vlastních skriptů na virtuálních počítačích, které běží na zařízení Azure Stack Edge pro pomocí šablon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 2d2e7d403ab3e9cc7e8e17de53b6e821ec24caa1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438008"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>Nasazení rozšíření vlastních skriptů na virtuálních počítačích běžících na zařízení Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Rozšíření vlastních skriptů stáhne a spustí skripty nebo příkazy na virtuálních počítačích běžících na zařízeních Azure Stack Edge pro. Tento článek podrobně popisuje, jak nainstalovat a spustit rozšíření vlastních skriptů pomocí šablony Azure Resource Manager. 

Tento článek se týká Azure Stackch PROCESORů Edge pro, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.

## <a name="about-custom-script-extension"></a>O rozšíření vlastních skriptů

Rozšíření vlastních skriptů je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakoukoli jinou úlohu konfigurace nebo správy. Můžete stáhnout skripty z Azure Storage nebo jiného přístupného internetového umístění, nebo můžete do modulu runtime rozšíření poskytnout skripty nebo příkazy.

Rozšíření vlastních skriptů se integruje s Azure Resource Manager šablonami. Můžete ho také spustit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Virtual Machines REST API.

## <a name="os-for-custom-script-extension"></a>Operační systém pro rozšíření vlastních skriptů

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Podporovaný operační systém pro rozšíření vlastních skriptů ve Windows

Rozšíření vlastních skriptů pro Windows se spustí v následujících OSs. Jiné verze mohou fungovat, ale nebyly testovány na virtuálních počítačích běžících na zařízeních Azure Stack Edge pro.

| Distribuce | Verze |
|---|---|
| Windows Server 2019 | Jádro |
| Windows Server 2016 | Jádro |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Podporovaný operační systém pro rozšíření vlastních skriptů v systému Linux

Rozšíření vlastních skriptů pro Linux se spustí v následujících OSs. Jiné verze mohou fungovat, ale nebyly testovány na virtuálních počítačích běžících na zařízeních Azure Stack Edge pro.

| Distribuce | Verze |
|---|---|
| Linux: Ubuntu | 18,04 LTS |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>Předpoklady

1. [Stáhněte si šablony virtuálních počítačů a soubory parametrů](https://aka.ms/ase-vm-templates) do klientského počítače. Rozbalte stáhnout do adresáře, který budete používat jako pracovní adresář.

1. V zařízení byste měli mít vytvořený a nasazený virtuální počítač. Pokud chcete vytvořit virtuální počítače, postupujte podle všech kroků v části [nasazení virtuálního počítače na Azure Stack Edge pro pomocí šablon](azure-stack-edge-gpu-deploy-virtual-machine-templates.md).

    Pokud potřebujete stáhnout skript, jako je například z GitHubu nebo Azure Storage externě, při konfiguraci výpočetní sítě povolte port, který je připojen k Internetu pro výpočetní výkon. To vám umožní stáhnout skript.

    V následujícím příkladu byl port 2 připojen k Internetu a byl použit k povolení výpočetní sítě. Pokud jste v předchozím kroku zjistili, že Kubernetes není potřeba, můžete přeskočit přiřazení IP adresy uzlu Kubernetes a externích služeb.

    ![Povolit výpočetní nastavení na portu připojeném k Internetu](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>Nainstalovat rozšíření vlastních skriptů

V závislosti na operačním systému pro váš virtuální počítač můžete nainstalovat rozšíření vlastních skriptů pro Windows nebo Linux.
 

### <a name="custom-script-extension-for-windows"></a>Rozšíření vlastních skriptů pro virtuální počítače

Pokud chcete nasadit rozšíření vlastních skriptů pro Windows pro virtuální počítač běžící na vašem zařízení, upravte `addCSExtWindowsVM.parameters.json` soubor parametrů a pak šablonu nasaďte `addCSextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Upravit soubor parametrů

Soubor `addCSExtWindowsVM.parameters.json` má následující parametry:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Zadejte název virtuálního počítače, název rozšíření a příkaz, který chcete spustit.

Zde je ukázkový soubor s parametrem, který byl použit v tomto článku.

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>Nasazení šablony

Nasaďte šablonu `addCSextensiontoVM.json` . Tato šablona nasadí rozšíření na existující virtuální počítač. Spusťte následující příkaz:

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> Nasazení rozšíření je dlouhodobá úloha, která trvá přibližně 10 minut, než se dokončí.

Zde je ukázkový výstup:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>Sledovat nasazení

Pokud chcete zjistit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
Zde je ukázkový výstup:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Po dokončení nasazení se `ProvisioningState` změní na `Succeeded` .

Výstup rozšíření je protokolován do souborů nalezených v následující složce na cílovém virtuálním počítači. 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Zadané soubory jsou staženy do následující složky v cílovém virtuálním počítači.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
kde <n> je desítkové celé číslo, které se může změnit mezi spouštěním rozšíření. Hodnota 1. * odpovídá skutečné aktuální `typeHandlerVersion` hodnotě rozšíření. Například skutečný adresář v této instanci byl `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` . 


V tomto případě se jedná o příkaz, který se spustí pro vlastní rozšíření: `md C:\\Users\\Public\\Documents\\test` . Po úspěšné instalaci rozšíření můžete ověřit, že se adresář vytvořil na virtuálním počítači v zadané cestě v příkazu. 


### <a name="custom-script-extension-for-linux"></a>Rozšíření vlastních skriptů pro Linux

Pokud chcete nasadit rozšíření vlastních skriptů pro Windows pro virtuální počítač běžící na vašem zařízení, upravte `addCSExtLinuxVM.parameters.json` soubor parametrů a pak šablonu nasaďte `addCSExtensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Upravit soubor parametrů

Soubor `addCSExtLinuxVM.parameters.json` má následující parametry:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
Zadejte název virtuálního počítače, název rozšíření a příkaz, který chcete spustit.

Tady je ukázkový soubor s parametry, který se použil v tomto článku:

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> Nasazení rozšíření je dlouhodobá úloha, která trvá přibližně 10 minut, než se dokončí.

Zde je ukázkový výstup:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`Byl nastaven pro vytvoření souboru `file2.txt` v `/home/Administrator` adresáři a obsah souboru `some text` . V takovém případě můžete ověřit, že byl soubor vytvořen v zadané cestě.

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>Sledovat stav nasazení    
    
Template deployment je dlouho běžící úloha. Pokud chcete zjistit stav nasazení rozšíření pro daný virtuální počítač, otevřete jinou relaci PowerShellu (Spustit jako správce). Spusťte následující příkaz: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Zde je ukázkový výstup: 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> Po dokončení nasazení se `ProvisioningState` změní na `Succeeded` .

Výstup spuštění rozšíření se zaznamená do následujícího souboru: `/var/lib/waagent/custom-script/download/0/` .


## <a name="remove-custom-script-extension"></a>Odebrat rozšíření vlastních skriptů

Chcete-li odebrat rozšíření vlastních skriptů, použijte následující příkaz:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Zde je ukázkový výstup:

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>Další kroky

[Rutiny Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
