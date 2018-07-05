---
title: Rozšíření Azure DSC pro Linux | Dokumentace Microsoftu
description: Nainstaluje balíčky OMI a DSC umožňující virtuálního počítače Azure s Linuxem nakonfigurovat Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: balukambala
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: e0bf18cf6736463740f3ab5c33830dcf84bc0817
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449791"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozšíření DSC pro Linux (Microsoft.OSTCExtensions.DSCForLinux)

## <a name="overview"></a>Přehled

Desired State Configuration (DSC) je platforma pro správu, který umožňuje spravovat vaši IT a rozvoj infrastruktury s konfigurací jako kód.

Rozšíření DSCForLinux je publikována a podporované společností Microsoft. Rozšíření nainstaluje agenta OMI a DSC na virtuálních počítačích Azure. Rozšíření DSC můžete také provést následující akce


- Zaregistrovat virtuálního počítače s Linuxem do účtu Azure Automation, abyste mohli o přijetí změn konfigurace ze služby Azure Automation (zaregistrovat ExtensionAction)
- Konfigurace MOF nabízených oznámení do virtuálního počítače s Linuxem (Push ExtensionAction)
- Použijte Meta MOF konfiguraci virtuálního počítače s Linuxem ke konfiguraci serveru vyžádané replikace, aby bylo možné o přijetí změn konfigurace uzlu (ExtensionAction o přijetí změn)
- Instalace vlastních modulů DSC virtuálního počítače s Linuxem (ExtensionAction instalace)
- Odebrat vlastní moduly DSC do virtuálního počítače s Linuxem (odebrat ExtensionAction)

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC Linuxu podporuje všechny [rozšíření nepodporuje OS](https://azurewiki.cloudapp.netVMAgentExtension/extensionSupportedOSs) s výjimkou:

| Distribuce | Verze |
|---|---|
| CentOS Linux | 6,5 a vyšší |
| Ubuntu| 12.04 LTS, 14.04 LTS, 16.04 LTS  |
| RHEL| 6,5 a vyšší  |
| openSUSE| 13.1 nebo vyšší  |
| SUSE Linux Enterprise Server| 11 SP3 nebo novější  |

  
 
### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSCForLinux vyžaduje, aby cílový virtuální počítač je připojený k Internetu. Například registrace rozšíření vyžaduje připojení ke službě Automation. Instalace pro další akce, třeba o přijetí změn, o přijetí změn, vyžaduje připojení k azure úložiště nebo github. To závisí na nastavení od zákazníků.

## <a name="extension-schema"></a>Schéma rozšíření

### <a name="11-public-configuration"></a>1.1 veřejné konfigurace

Tady jsou všechny parametry podporované veřejné konfigurace:

* `FileUri`: (volitelné, string) uri KOMPRIMOVANÉHO souboru MOF souboru/Meta MOF souboru/vlastní prostředek.
* `ResourceName`: (volitelné, string) název modulu vlastních prostředků
* `ExtensionAction`: (volitelné, string) určuje, co dělá rozšíření. Platné hodnoty: zaregistrovat, Push, Pull, nainstalovat, odebrat. Pokud není zadán, považují se za nabízená akce ve výchozím nastavení.
* `NodeConfigurationName`: (volitelné, string) název konfigurace uzlu, který chcete použít.
* `RefreshFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) DSC se pokusí získat konfiguraci ze serveru vyžádané replikace. 
       Pokud je konfigurace na serveru vyžádané replikace se liší od aktuálního na cílový uzel, je zkopírován do čeká na úložiště a použít.
* `ConfigurationMode`: (volitelné, string) určuje, jak by měl DSC použít danou konfiguraci. Platné hodnoty jsou: ApplyOnly ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) DSC se zajistí, že konfigurace je v požadovaném stavu.

> [!NOTE]
> Pokud používáte verzi < 2.3, je stejná jako ExtensionAction parametr mode. Režim se zdá být přetížená termín. Abyste předešli zmatení, proto se ExtensionAction používá od verze 2.3 a vyšší. Rozšíření pro zpětnou kompatibilitu, podporuje režim a ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 chráněné konfigurace

Tady jsou všechny podporované chráněné konfigurační parametry:

* `StorageAccountName`: (volitelné, string) název účtu úložiště, která obsahuje soubor
* `StorageAccountKey`: (volitelné, string) klíč účtu úložiště, která obsahuje soubor
* `RegistrationUrl`: (volitelné, string) adresu URL účtu Azure Automation.
* `RegistrationKey`: (volitelné, string) přístupový klíč účtu služby Azure Automation


## <a name="scenarios"></a>Scénáře

### <a name="register-to-azure-automation-account"></a>Zaregistrujte se a účet Azure Automation
Protected.JSON
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
Public.JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formát prostředí PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru MOF (v účtu úložiště Azure) k virtuálnímu počítači

Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public.JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formát prostředí PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru MOF (ve veřejné úložiště) k virtuálnímu počítači

Public.JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formát prostředí PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru MOF meta (v účtu úložiště Azure) k virtuálnímu počítači

Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public.JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formát prostředí PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru MOF meta (ve veřejné úložiště) k virtuálnímu počítači
Public.JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formát prostředí PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Instalace modulu vlastní prostředek (soubor ZIP v účtu úložiště Azure) k virtuálnímu počítači
Protected.JSON
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
Public.JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formát prostředí PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalace modulu vlastní prostředek (soubor ZIP do veřejného úložiště) k virtuálnímu počítači
Public.JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formát prostředí PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Odebrání modulu vlastní prostředek z virtuálního počítače
Public.JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formát prostředí PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazování jedné nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je připojení ke službě Azure Automation. 

Je ukázka šablony Resource Manageru [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) a [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Další podrobnosti o šablony Azure Resource Manageru, najdete na webu [šablon pro vytváření Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

### <a name="21-using-azure-cliazure-cli"></a>2.1. Použití [**rozhraní příkazového řádku Azure**] [azure-cli]
Před nasazením DSCForLinux rozšíření, měli byste nakonfigurovat váš `public.json` a `protected.json`, podle různých scénářů v oddílu 3.

#### <a name="211-classic"></a>2.1.1. Classic
Klasickém režimu se také nazývá režimu Azure Service Management. Můžete přepnout na jeho spuštěním:
```
$ azure config mode asm
```

Můžete nasadit rozšíření DSCForLinux spuštěním:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Přečtěte si nejnovější verzi rozšíření, která je k dispozici, spusťte:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Můžete přepnout do režimu Azure Resource Manageru spuštěním:
```
$ azure config mode arm
```

Můžete nasadit rozšíření DSCForLinux spuštěním:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> V režimu Azure Resource Manageru `azure vm extension list` zatím není k dispozici.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Použití [**prostředí Azure PowerShell**] [azure-powershell]

#### <a name="221-classic"></a>2.2.1 classic

Ke svému účtu Azure (v režimu Azure Service Management) se můžete přihlásit pomocí:

```powershell>
Add-AzureAccount
```

A nasazení rozšíření DSCForLinux spuštěním:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Je třeba změnit obsah $privateConfig a $publicConfig podle různých scénářích ve výše uvedené části 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2.Resource správce

Ke svému účtu Azure (v režimu Azure Resource Manageru) se můžete přihlásit pomocí:

```powershell>
Login-AzureRmAccount
```

Klikněte na tlačítko [ **TADY** ](../../azure-resource-manager/powershell-azure-resource-manager.md) Další informace o tom, jak pomocí Azure Powershellu s Azure Resource Manageru.

Můžete nasadit rozšíření DSCForLinux spuštěním:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Je třeba změnit obsah $privateConfig a $publicConfig podle různých scénářích ve výše uvedené části 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureRmVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí rozhraní příkazového řádku Azure. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Rozšíření provádění výstup je zaznamenán do následujícího souboru:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kód chyby: 51 představuje nepodporované distribuce nebo nepodporované rozšíření akce.
V některých případech DSC Linux rozšíření se nedaří nainstalovat OMI vyšší verze infrastruktury OMI je již existuje v počítači. [odpovědi na chybu: (000003) Downgrade nepovoluje]



### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup
Další informace o rozšířeních najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Linux](features-linux.md).
