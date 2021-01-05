---
title: Přehled a nasazení virtuálních počítačů GPU na zařízení Azure Stack Edge pro
description: Popisuje, jak vytvořit a spravovat virtuální počítače GPU na zařízení Azure Stack Edge pro pomocí šablon.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: 0a7f54d991d31594a6ab522dbf9e73958cde3023
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900140"
---
# <a name="gpu-vms-for-your-azure-stack-edge-pro-device"></a>Virtuální počítače GPU pro zařízení Azure Stack Edge pro

Tento článek poskytuje přehled virtuálních počítačů GPU (VM) na zařízení Azure Stack Edge pro. Tento článek popisuje, jak vytvořit virtuální počítač GPU a pak nainstalovat rozšíření ovladače GPU pro instalaci příslušných ovladačů NVIDIA. Pomocí šablon Azure Resource Manager vytvořte virtuální počítač GPU a nainstalujte rozšíření ovladače GPU. 

Tento článek se týká Azure Stack GPU pro procesory Edge a Azure Stack Edge pro R.

## <a name="about-gpu-vms"></a>O virtuálních počítačích GPU

Vaše zařízení Azure Stack Edge pro jsou vybavená 1 nebo 2 grafickým procesorem NVIDIA Tesla T4. Pokud chcete na těchto zařízeních nasadit úlohy s akcelerovaným grafickým procesorem, použijte velikost virtuálních počítačů optimalizované pro GPU. Například pro nasazení odvozených úloh s grafickým procesorem T4 by se měla použít řada NC V3 v3. 

Další informace najdete v tématu [virtuální počítače síťového adaptéru s T4 a 3 Series](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Podporované operační systémy a ovladače GPU 

Aby bylo možné využít možnosti GPU pro virtuální počítače řady Azure N-Series, je nutné nainstalovat ovladače GPU NVIDIA. 

Rozšíření ovladače NVIDIA GPU nainstaluje vhodné ovladače NVIDIA CUDA nebo GRID. Rozšíření můžete nainstalovat nebo spravovat pomocí Azure Resource Manager šablon.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Podporovaný operační systém pro rozšíření GPU pro Windows

Toto rozšíření podporuje následující operační systémy (OSs). Jiné verze mohou fungovat, ale nebyly testovány interně na virtuální počítače GPU běžící na zařízeních Azure Stack Edge pro.

| Distribuce | Verze |
|---|---|
| Windows Server 2019 | Jádro |
| Windows Server 2016 | Jádro |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Podporovaný operační systém pro rozšíření GPU pro Linux

Toto rozšíření podporuje následující distribuce operačního systému, a to v závislosti na podpoře ovladače pro konkrétní verzi operačního systému. Jiné verze mohou fungovat, ale nebyly testovány interně na virtuální počítače GPU běžící na zařízeních Azure Stack Edge pro.


| Distribuce | Verze |
|---|---|
| Ubuntu | 18,04 LTS |
| Red Hat Enterprise Linux | 7,4 |


## <a name="gpu-vms-and-kubernetes"></a>Virtuální počítače GPU a Kubernetes

Než na zařízení nasadíte virtuální počítače GPU, přečtěte si následující skutečnosti, pokud je na zařízení nakonfigurovaný Kubernetes.

#### <a name="for-1-gpu-device"></a>Pro zařízení s jedním procesorem GPU: 

- **Vytvořte na svém zařízení virtuální počítač GPU následovaný konfigurací Kubernetes**: v tomto scénáři bude vytváření virtuálních počítačů GPU a konfigurace Kubernetes úspěšné. Kubernetes nebude mít v tomto případě přístup k GPU.

- **Nakonfigurujte na svém zařízení Kubernetes a pak vytvořte virtuální počítač GPU**: v tomto scénáři KUBERNETES uplatní GPU na vašem zařízení a vytvoření virtuálního počítače selže, protože nejsou k dispozici žádné prostředky GPU.

#### <a name="for-2-gpu-device"></a>Pro zařízení se dvěma procesory GPU

- **Vytvořte na svém zařízení virtuální počítač GPU následovaný konfigurací Kubernetes**: v tomto scénáři bude virtuální počítač GPU, který vytvoříte, vyžádat jeden grafický procesor v zařízení a konfigurace Kubernetes bude taky úspěšná a vyžádat si zbývající procesor. 

- **Vytvoření dvou virtuálních počítačů s grafickým procesorem následovaných konfigurací Kubernetes na zařízení**: v tomto scénáři budou dva virtuální počítače GPU nárokovat na tyto dvě GPU na zařízení a Kubernetes se úspěšně nakonfigurují bez GPU. 

- **Nakonfigurujte na svém zařízení Kubernetes a pak vytvořte virtuální počítač GPU**: v tomto scénáři bude Kubernetes na svém zařízení uplatňovat GPU a vytvoření virtuálního počítače selže, protože nejsou k dispozici žádné prostředky GPU.

Pokud máte na svém zařízení nainstalované virtuální počítače s grafickým procesorem a Kubernetes se taky nakonfigurují, pak dojde k jejich navýšení na virtuální počítač (když zastavíte nebo odeberete virtuální počítač pomocí Stop-AzureRmVM nebo odebrat-AzureRmVM), existuje riziko, že cluster Kubernetes bude uplatňovat všechny GPU dostupné v zařízení. V takovém případě nebudete moci restartovat virtuální počítače GPU nasazené v zařízení nebo vytvořit virtuální počítače GPU.


## <a name="create-gpu-vms"></a>Vytváření virtuálních počítačů GPU

Při nasazení virtuálních počítačů GPU do zařízení postupujte podle těchto kroků:

1. Určete, jestli bude vaše zařízení také používat Kubernetes. Pokud bude zařízení používat Kubernetes, musíte nejdřív vytvořit virtuální počítač GPU a potom nakonfigurovat Kubernetes. Pokud je nejdřív nakonfigurovaný Kubernetes, vyžádá všechny dostupné prostředky GPU a vytvoření virtuálního počítače GPU se nezdaří.

1. [Stáhněte si šablony virtuálních počítačů a soubory parametrů](https://aka.ms/ase-vm-templates) do klientského počítače. Rozbalte ho do adresáře, který použijete jako pracovní adresář.

1. Pokud chcete vytvořit virtuální počítače GPU, postupujte podle všech kroků v části [nasazení virtuálního počítače na Azure Stack Edge pro pomocí šablon](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) s výjimkou následujících rozdílů: 

    1. Při konfiguraci výpočetní sítě povolte službu COMPUTE port, který je připojený k Internetu. To vám umožní stáhnout ovladače GPU vyžadované pro rozšíření GPU pro virtuální počítače GPU.

        Tady je příklad, kdy byl port 2 připojen k Internetu a byl použit k povolení výpočetní sítě. Pokud jste zjistili, že v předchozím kroku není potřeba Kubernetes, můžete přeskočit přiřazení IP adresy uzlu Kubernetes a externích služeb.

        ![Povolit výpočetní nastavení na portu připojeném k Internetu](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

            
    1. Vytvořte virtuální počítač pomocí šablon. Při zadávání velikosti virtuálních počítačů GPU se ujistěte, že používáte řady NCasT4-V3-Series v rozhraní, které `CreateVM.parameters.json` jsou podporované pro virtuální počítače GPU. Další informace najdete v tématu [podporované velikosti virtuálních počítačů pro virtuální počítače GPU](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```

    1. Jakmile se virtuální počítač GPU úspěšně vytvoří, můžete tento virtuální počítač zobrazit v seznamu virtuálních počítačů v prostředku Azure Stack Edge v Azure Portal.

        ![Virtuální počítač GPU v seznamu virtuálních počítačů v Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Vyberte virtuální počítač a přejděte k podrobnostem. Zkopírujte IP adresu přiřazenou k virtuálnímu počítači.

    ![IP adresa přidělená virtuálnímu počítači GPU v Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Po vytvoření virtuálního počítače nasaďte rozšíření GPU pomocí šablony rozšíření. Informace o virtuálních počítačích se systémem Linux najdete v tématu instalace [rozšíření GPU pro Linux](#gpu-extension-for-linux) a pro virtuální počítače s Windows najdete v tématu [Instalace rozšíření GPU pro Windows](#gpu-extension-for-windows).

1. Pokud chcete ověřit instalaci rozšíření GPU, připojte se k virtuálnímu počítači GPU:
    1. Pokud používáte virtuální počítač s Windows, postupujte podle kroků v části [připojení k virtuálnímu počítači s Windows](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-windows-vm). [Ověřte instalaci](#verify-windows-driver-installation).
    1. Pokud používáte virtuální počítač se systémem Linux, postupujte podle kroků v části [připojení k virtuálnímu počítači se systémem Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). [Ověřte instalaci](#verify-linux-driver-installation).

1. V případě potřeby můžete výpočetní síť přepnout zpátky na cokoli, co potřebujete. 


> [!NOTE]
> Když aktualizujete verzi softwaru zařízení z 2012 na novější, budete muset virtuální počítače GPU zastavit ručně.


## <a name="install-gpu-extension"></a>Nainstalovat rozšíření GPU

V závislosti na operačním systému pro váš virtuální počítač můžete nainstalovat rozšíření GPU pro Windows nebo Linux.

> [!NOTE]
> Než nainstalujete rozšíření GPU, ujistěte se, že port povolený pro výpočetní síť v zařízení je připojený k Internetu a má přístup. Ovladače GPU se stáhnou přes přístup k Internetu.

### <a name="gpu-extension-for-windows"></a>Rozšíření GPU pro Windows

Pro nasazení ovladačů NVIDIA GPU pro existující virtuální počítač upravte `addGPUExtWindowsVM.parameters.json` soubor parametrů a pak šablonu nasaďte `addGPUextensiontoVM.json` .

#### <a name="edit-parameters-file"></a>Upravit soubor parametrů

Soubor `addGPUExtWindowsVM.parameters.json` má následující parametry:

```json
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: windowsGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverWindows" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DriverURL" : "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
    "DriverCertificateUrl" : "https://go.microsoft.com/fwlink/?linkid=871664",
    "DriverType":"CUDA"
    }
    }
    }
```

Tady je ukázkový soubor s parametry, který se použil v tomto článku:

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment2"

DeploymentName          : deployment2
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:02:56 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          adminUsername    String                     Administrator
                          password         String                     Password1
                          imageName        String                     myasewindowsimg
                          vmSize           String                     Standard_NC4as_T4_v3
                          vnetName         String                     ASEVNET
                          vnetRG           String                     aserg
                          subnetName       String                     ASEVNETsubNet
                          nicName          String                     nic6
                          ipConfigName     String                     ipconfig6
                          privateIPAddress  String

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```
#### <a name="deploy-template"></a>Nasazení šablony

Nasaďte šablonu `addGPUextensiontoVM.json` . Tato šablona nasadí rozšíření na existující virtuální počítač. Spusťte následující příkaz:

```powershell
$templateFile = "<Path to addGPUextensiontoVM.json>" 
$templateParameterFile = "<Path to addGPUExtWindowsVM.parameters.json>" 
$RGName = "<Name of your resource group>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
```
> [!NOTE]
> Nasazení rozšíření je dlouhodobá úloha, která trvá přibližně 10 minut, než se dokončí.

Zde je ukázkový výstup:

```powershell
PS C:\WINDOWS\system32> "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment3"

DeploymentName          : deployment3
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/16/2020 12:18:50 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM2
                          extensionName    String                     windowsgpuext
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverWindows
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
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
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM2 -Name windowsgpuext

ResourceGroupName       : myasegpuvm1
VMName                  : VM2
Name                    : windowsgpuext
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverWindows
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM2/extensions/windowsgpuext
PublicSettings          : {
                            "DriverURL": "http://us.download.nvidia.com/tesla/442.50/442.50-tesla-desktop-winserver-2019-2016-international.exe",
                            "DriverCertificateUrl": "https://go.microsoft.com/fwlink/?linkid=871664",
                            "DriverType": "CUDA"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

Výstup spuštění rozšíření je protokolován do následujícího souboru. Pokud chcete `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status` sledovat stav instalace, podívejte se na tento soubor. 


Úspěšná instalace je označena `message` jako `Enable Extension` a `status` jako `success` .

```powershell
"status":  {
                       "formattedMessage":  {
                                                "message":  "Enable Extension",
                                                "lang":  "en"
                                            },
                       "name":  "NvidiaGpuDriverWindows",
                       "status":  "success",
```

#### <a name="verify-windows-driver-installation"></a>Ověřit instalaci ovladače Windows

Přihlaste se k virtuálnímu počítači a spusťte nástroj příkazového řádku NVIDIA-SMI nainstalovaný s ovladačem. `nvidia-smi.exe`Je umístěný na adrese `C:\Program Files\NVIDIA Corporation\NVSMI\nvidia-smi.exe` . Pokud soubor nevidíte, je možné, že je instalace ovladače pořád spuštěná na pozadí. Počkejte 10 minut a zkuste to znovu.

Pokud je ovladač nainstalovaný, zobrazí se výstup podobný následujícímu příkladu: 

```powershell
PS C:\Users\Administrator> cd "C:\Program Files\NVIDIA Corporation\NVSMI"
PS C:\Program Files\NVIDIA Corporation\NVSMI> ls

    Directory: C:\Program Files\NVIDIA Corporation\NVSMI

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        2/26/2020  12:00 PM         849640 MCU.exe
-a----        2/26/2020  12:00 PM         443104 nvdebugdump.exe
-a----        2/25/2020   2:06 AM          81823 nvidia-smi.1.pdf
-a----        2/26/2020  12:01 PM         566880 nvidia-smi.exe
-a----        2/26/2020  12:01 PM         991344 nvml.dll

PS C:\Program Files\NVIDIA Corporation\NVSMI> .\nvidia-smi.exe
Wed Dec 16 00:35:51 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 442.50       Driver Version: 442.50       CUDA Version: 10.2     |
|-------------------------------+----------------------+----------------------+
| GPU  Name            TCC/WDDM | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            TCC  | 0000503C:00:00.0 Off |                    0 |
| N/A   35C    P8    11W /  70W |      8MiB / 15205MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
PS C:\Program Files\NVIDIA Corporation\NVSMI>
```

Další informace najdete v tématu [rozšíření ovladače NVIDIA GPU pro systém Windows](../virtual-machines/extensions/hpccompute-gpu-windows.md).

### <a name="gpu-extension-for-linux"></a>Rozšíření GPU pro Linux

Pro nasazení ovladačů NVIDIA GPU pro existující virtuální počítač upravte soubor parametrů a pak šablonu nasaďte `addGPUextensiontoVM.json` . K dispozici jsou konkrétní soubory parametrů pro Ubuntu a Red Hat Enterprise Linux (RHEL), jak je popsáno v následujících částech.

#### <a name="edit-parameters-file"></a>Upravit soubor parametrů

Pokud používáte Ubuntu, `addGPUExtLinuxVM.parameters.json` soubor převezme následující parametry:

```powershell
"parameters": { 
    "vmName": {
    "value": "<name of the VM>" 
    },
    "extensionName": {
    "value": "<name for the extension. Example: linuxGpu>" 
    },
    "publisher": {
    "value": "Microsoft.HpcCompute" 
    },
    "type": {
    "value": "NvidiaGpuDriverLinux" 
    },
    "typeHandlerVersion": {
    "value": "1.3" 
    },
    "settings": {
    "value": {
    "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
    "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
    "CUDA_ver": "10.0.130",
    "InstallCUDA": "true"
    }
    }
    }
```
Pokud používáte Red Hat Enterprise Linux (RHEL), `addGPUExtensionRHELVM.parameters.json` soubor má následující parametry:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<name of the VM>" 
        },
        "extensionName": {
            "value": "<name for the extension. Example: linuxGpu>" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
                    "isCustomInstall":true,
                    "DRIVER_URL":"https://go.microsoft.com/fwlink/?linkid=874273",
                    "CUDA_ver":"10.0.130",
                    "PUBKEY_URL":"http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                    "DKMS_URL":"https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm",
                    "LIS_URL":"https://aka.ms/lis",
                    "LIS_RHEL_ver":"3.10.0-1062.9.1.el7"
            }
        }
    }
}
```


Tady je ukázkový soubor s parametrem Ubuntu, který se použil v tomto článku:

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM1" 
        },
        "extensionName": {
            "value": "gpuLinux" 
        },
        "publisher": {
            "value": "Microsoft.HpcCompute" 
        },
        "type": {
            "value": "NvidiaGpuDriverLinux" 
        },
        "typeHandlerVersion": {
            "value": "1.3" 
        },
        "settings": {
            "value": {
            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
            "CUDA_ver": "10.0.130",
            "InstallCUDA": "true"
            }
        }
    }
}
```


#### <a name="deploy-template"></a>Nasazení šablony

Nasaďte šablonu `addGPUextensiontoVM.json` . Tato šablona nasadí rozšíření na existující virtuální počítač. Spusťte následující příkaz:

```powershell
$templateFile = "Path to addGPUextensiontoVM.json" 
$templateParameterFile = "Path to addGPUExtLinuxVM.parameters.json" 
$RGName = "<Name of your resource group>" 
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Name for your deployment>"
``` 

> [!NOTE]
> Nasazení rozšíření je dlouhodobá úloha, která trvá přibližně 10 minut, než se dokončí.

Zde je ukázkový výstup:

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addGPUextensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addGPUExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "rg2"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "delpoyment7"

DeploymentName          : delpoyment7
ResourceGroupName       : rg2
ProvisioningState       : Succeeded
Timestamp               : 12/10/2020 10:43:23 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM1
                          extensionName    String                     gpuLinux
                          publisher        String                     Microsoft.HpcCompute
                          type             String                     NvidiaGpuDriverLinux
                          typeHandlerVersion  String                     1.3
                          settings         Object                     {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL":
                          "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
                          }

Outputs                 :
DeploymentDebugLogLevel :
PS C:\WINDOWS\system32>
```

#### <a name="track-deployment-status"></a>Sledovat stav nasazení    
    
Template deployment je dlouho běžící úloha. Pokud chcete zjistit stav nasazení rozšíření pro daný virtuální počítač, otevřete jinou relaci PowerShellu (Spustit jako správce). Spusťte následující příkaz: 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
Zde je ukázkový výstup: 

```powershell
Copyright (C) Microsoft Corporation. All rights reserved.
Try the new cross-platform PowerShell https://aka.ms/pscore6

PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName rg2 -VMName VM1 -Name gpulinux

ResourceGroupName       : rg2
VMName                  : VM1
Name                    : gpuLinux
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.HpcCompute
ExtensionType           : NvidiaGpuDriverLinux
TypeHandlerVersion      : 1.3
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/rg2/providers/Microsoft.Compute/virtualMachines/VM1/extensions/gpuLinux
PublicSettings          : {
                            "DRIVER_URL": "https://go.microsoft.com/fwlink/?linkid=874271",
                            "PUBKEY_URL": "http://download.microsoft.com/download/F/F/A/FFAC979D-AD9C-4684-A6CE-C92BB9372A3B/7fa2af80.pub",
                            "CUDA_ver": "10.0.130",
                            "InstallCUDA": "true"
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

Výstup spuštění rozšíření se zaznamená do následujícího souboru: `/var/log/azure/nvidia-vmext-status` .

#### <a name="verify-linux-driver-installation"></a>Ověřit instalaci ovladače pro Linux

Pomocí těchto kroků Ověřte instalaci ovladače:

1. Připojte se k virtuálnímu počítači GPU. Postupujte podle pokynů v tématu [připojení k virtuálnímu počítači se systémem Linux](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-linux-vm). 

    Zde je ukázkový výstup:

    ```powershell
    PS C:\WINDOWS\system32> ssh -l Administrator 10.57.50.60
    Administrator@10.57.50.60's password:
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage
      System information as of Thu Dec 10 22:57:01 UTC 2020
    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
    
    249 packages can be updated.
    140 updates are security updates.
    
    Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 5.0.0-1031-azure x86_64)    
     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage    
      System information as of Thu Dec 10 22:57:01 UTC 2020    
      System load:  0.0                Processes:           133
      Usage of /:   24.8% of 28.90GB   Users logged in:     0
      Memory usage: 2%                 IP address for eth0: 10.57.50.60
      Swap usage:   0%
        
    249 packages can be updated.
    140 updates are security updates.
    
    New release '20.04.1 LTS' available.
    Run 'do-release-upgrade' to upgrade to it.
        
    *** System restart required ***
    Last login: Thu Dec 10 21:49:29 2020 from 10.90.24.23
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.
    
    Administrator@VM1:~$
    ```
2. Spusťte nástroj příkazového řádku NVIDIA-SMI nainstalovaný s ovladačem. Pokud je ovladač úspěšně nainstalován, budete moci spustit nástroj a zobrazit následující výstup:

    ```powershell
    Administrator@VM1:~$ nvidia-smi
    Thu Dec 10 22:58:46 2020
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 455.45.01    Driver Version: 455.45.01    CUDA Version: 11.1     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            Off  | 0000941F:00:00.0 Off |                    0 |
    | N/A   48C    P0    27W /  70W |      0MiB / 15109MiB |      5%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    Administrator@VM1:~$
    ```

Další informace najdete v tématu [rozšíření ovladače NVIDIA GPU pro Linux](../virtual-machines/extensions/hpccompute-gpu-linux.md).

## <a name="remove-gpu-extension"></a>Odebrat rozšíření GPU

Chcete-li odebrat rozšíření GPU, použijte následující příkaz:

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

Zde je ukázkový výstup:

```powershell
PS C:\azure-stack-edge-deploy-vms> Remove-AzureRmVMExtension -ResourceGroupName rgl -VMName WindowsVM -Name windowsgpuext
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue? [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
Requestld IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------    
          True                OK         OK
```




## <a name="next-steps"></a>Další kroky

[Rutiny Azure Resource Manager](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)