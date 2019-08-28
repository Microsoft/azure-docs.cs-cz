---
title: Rozšíření Azure DSC pro Linux
description: Nainstaluje OMI a balíčky DSC, které umožní nakonfigurovat virtuální počítač Azure Linux pomocí konfigurace požadovaného stavu.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: c37b81e08e5d9f150081a9dc12af51175e3f590c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084724"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozšíření DSC pro Linux (Microsoft. OSTCExtensions. DSCForLinux)

Požadovaná konfigurace stavu (DSC) je platforma pro správu, která umožňuje spravovat infrastrukturu IT a vývoj s konfigurací jako kód.

Rozšíření DSCForLinux je publikované a podporované Microsoftem. Rozšíření nainstaluje agenta OMI a DSC na virtuální počítače Azure. Rozšíření DSC může také provádět následující akce.


- Registrace virtuálního počítače se systémem Linux pro Azure Automation účtu pro vyžádání konfigurací ze služby Azure Automation (Register ExtensionAction)
- Vložení konfigurace MOF do virtuálního počítače se systémem Linux (push ExtensionAction)
- Pro virtuální počítač se systémem Linux použijte konfiguraci meta MOF a nakonfigurujete server Pull, aby bylo možné vyžádat konfiguraci uzlu (ExtensionAction pro vyžádání obsahu).
- Instalace vlastních modulů DSC do virtuálního počítače se systémem Linux (instalace ExtensionAction)
- Odebrat vlastní moduly DSC na virtuální počítač se systémem Linux (odebrat ExtensionAction)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC pro Linux podporuje všechny [distribuce systému Linux schválené v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) s výjimkou těchto:

| Distribuce | Version |
|---|---|
| Debian | všechny verze |
| Ubuntu| 18,04 |
 
### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSCForLinux vyžaduje, aby byl cílový virtuální počítač připojený k Internetu. Například registrovat rozšíření vyžaduje připojení ke službě Automation. Pro jiné akce, jako je například vyžádání, vyžádání, instalace vyžaduje připojení ke službě Azure Storage nebo GitHub. Závisí na nastaveních poskytovaných zákazníkem.

## <a name="extension-schema"></a>Schéma rozšíření

### <a name="11-public-configuration"></a>1,1 – veřejná konfigurace

Tady jsou všechny podporované parametry veřejné konfigurace:

* `FileUri`: (volitelné, String) identifikátor URI souboru MOF/souboru meta MOF nebo souboru ZIP vlastního prostředku.
* `ResourceName`: (volitelné, String) název vlastního modulu prostředků
* `ExtensionAction`: (volitelné, String) určuje, co rozšíření dělá. platné hodnoty: Registrace, vložení, stažení, instalace, odebrání. Pokud není zadaný, považuje se ve výchozím nastavení za nabízenou akci.
* `NodeConfigurationName`: (volitelné, String) název konfigurace uzlu, která se má použít.
* `RefreshFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) se DSC pokusí získat konfiguraci ze serveru vyžádané replikace. 
       Pokud se konfigurace na serveru pro vyžádání liší od aktuální položky na cílovém uzlu, zkopíruje se do úložiště čeká na vyřízení a použije se.
* `ConfigurationMode`: (volitelné, řetězec) určuje, jak má DSC použít konfiguraci. Platné hodnoty jsou: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) DSC ověří, jestli je konfigurace v požadovaném stavu.

> [!NOTE]
> Pokud používáte verzi < 2,3, parametr Mode je stejný jako ExtensionAction. Režim se jeví jako přetížený termín. Proto se k tomu vyhnete nejasnostem, ExtensionAction se používá od verze 2,3 a vyšší. Z důvodu zpětné kompatibility podporuje rozšíření jak režim, tak ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1,2 – chráněná konfigurace

Tady jsou všechny podporované parametry chráněné konfigurace:

* `StorageAccountName`: (volitelné, řetězec) název účtu úložiště, který obsahuje soubor.
* `StorageAccountKey`: (volitelné, String) klíč účtu úložiště, který obsahuje soubor
* `RegistrationUrl`: (volitelné, String) adresa URL účtu Azure Automation
* `RegistrationKey`: (volitelné, String) přístupový klíč účtu Azure Automation


## <a name="scenarios"></a>Scénáře

### <a name="register-to-azure-automation-account"></a>Zaregistrujte se do Azure Automation účtu.
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formát PowerShellu
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru MOF (v Azure Storage účtu) k virtuálnímu počítači

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formát PowerShellu
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru MOF (ve veřejném úložišti) na virtuální počítač

Public. JSON
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formát PowerShellu
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru meta MOF (v Azure Storage účtu) k virtuálnímu počítači

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

Public. JSON
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formát PowerShellu
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru meta MOF (ve veřejném úložišti) na virtuální počítač
Public. JSON
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formát PowerShellu
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor ZIP v Azure Storage účtu) do virtuálního počítače
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formát PowerShellu
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor ZIP ve veřejném úložišti) do virtuálního počítače
Public. JSON
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formát PowerShellu
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Odebrání vlastního modulu prostředků z virtuálního počítače
Public. JSON
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formát PowerShellu
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je připojování k Azure Automation. 

Vzorová Správce prostředků šablona je [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) a [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Další informace o šabloně Azure Resource Manager najdete v [Azure Resource Manager vytváření šablon](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Nasazení v Azure CLI

### <a name="21-using-azure-cliazure-cli"></a>2.1. Použití [**Azure CLI**] [Azure-CLI]
Před nasazením rozšíření DSCForLinux byste měli nakonfigurovat `public.json` a `protected.json`, podle různých scénářů v oddílu 3.

#### <a name="211-classic"></a>2.1.1. Classic
Klasický režim se taky označuje jako režim správy služeb Azure. Můžete na něj přejít spuštěním:
```
$ azure config mode asm
```

Rozšíření DSCForLinux můžete nasadit spuštěním:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Pokud se chcete dozvědět, jak nejnovější dostupná verze rozšíření, spusťte příkaz:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Můžete přepnout na režim Azure Resource Manager spuštěním:
```
$ azure config mode arm
```

Rozšíření DSCForLinux můžete nasadit spuštěním:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> V režimu `azure vm extension list` Azure Resource Manager není nyní k dispozici.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Použití [**Azure PowerShell**] [Azure-PowerShell]

#### <a name="221-classic"></a>2.2.1 – klasický

Můžete se přihlásit ke svému účtu Azure (režim správy služeb Azure) spuštěním:

```powershell>
Add-AzureAccount
```

A nasaďte rozšíření DSCForLinux spuštěním:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Je potřeba změnit obsah $privateConfig a $publicConfig podle různých scénářů v předchozích částech. 
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

#### <a name="222resource-manager"></a>2.2.2. Správce prostředků

Můžete se přihlásit k účtu Azure (režim Azure Resource Manager) spuštěním:

```powershell>
Login-AzAccount
```

Kliknutím [**sem**](../../azure-resource-manager/manage-resources-powershell.md) získáte další informace o použití Azure PowerShell s Azure Resource Manager.

Rozšíření DSCForLinux můžete nasadit spuštěním:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Je potřeba změnit obsah $privateConfig a $publicConfig podle různých scénářů v předchozích částech. 
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
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
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

Kód chyby: 51 představuje nepodporovanou akci distribuce nebo nepodporovanou příponu.
V některých případech se rozšíření DSC pro Linux nedokáže nainstalovat OMI, pokud už v počítači existuje vyšší verze OMI. [chybová odpověď: (000003) Downgrading není povolený]



### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/community/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další postup
Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).
