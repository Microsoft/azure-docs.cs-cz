---
title: Rozšíření Azure DSC pro Linux
description: Nainstaluje OMI a balíčky DSC, které umožní nakonfigurovat virtuální počítač Azure Linux pomocí konfigurace požadovaného stavu.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: mgoedtel
ms.author: magoedte
ms.collection: linux
ms.date: 06/12/2018
ms.openlocfilehash: 3a9478f93a2a6f8e31be39a58a18e0e394b9d5c2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560093"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozšíření DSC pro Linux (Microsoft. OSTCExtensions. DSCForLinux)

Požadovaná konfigurace stavu (DSC) je platforma pro správu, kterou můžete použít ke správě infrastruktury IT a vývoje s konfigurací jako kód.

> [!NOTE]
> Rozšíření DSC pro Linux a [rozšíření virtuálního počítače Log Analytics pro Linux](./oms-linux.md) aktuálně prezentují konflikt a nepodporuje se v souběžné konfiguraci. Nepoužívejte tato dvě řešení společně na stejném virtuálním počítači.

Rozšíření DSCForLinux je publikované a podporované Microsoftem. Rozšíření nainstaluje agenta OMI a DSC na virtuální počítače Azure. Rozšíření DSC může také provádět tyto akce:

- Zaregistrujte virtuální počítač Linux do účtu Azure Automation pro vyžádání konfigurací ze služby Azure Automation (Register ExtensionAction).
- Nahrajte konfigurace MOF do virtuálního počítače se systémem Linux (push ExtensionAction).
- Pomocí konfigurace meta MOF na virtuálním počítači se systémem Linux nakonfigurujte server vyžádané replikace, aby bylo možné vyžádat konfiguraci uzlu (ExtensionAction pro vyžádání obsahu).
- Nainstalujte vlastní moduly DSC do virtuálního počítače se systémem Linux (nainstalujte ExtensionAction).
- Odeberte vlastní moduly DSC z virtuálního počítače se systémem Linux (odebrat ExtensionAction).

## <a name="prerequisites"></a>Předpoklady

### <a name="operating-system"></a>Operační systém

Pro uzly se systémem Linux podporuje rozšíření DSC Linux všechna distribuce systému Linux uvedená v [dokumentaci k prostředí POWERSHELL DSC](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSCForLinux vyžaduje, aby byl cílový virtuální počítač připojený k Internetu. Například rozšíření registr vyžaduje připojení ke službě Automation.
Pro jiné akce, jako je například vyžádání, vyžádání, instalace vyžaduje připojení k Azure Storage a GitHubu. Závisí na nastaveních poskytovaných zákazníkem.

## <a name="extension-schema"></a>Schéma rozšíření

### <a name="public-configuration"></a>Veřejná konfigurace

Tady jsou všechny podporované parametry veřejné konfigurace:

* `FileUri`: (volitelné, String) identifikátor URI souboru MOF, soubor meta MOF nebo souboru ZIP vlastního prostředku.
* `ResourceName`: (volitelné, String) název vlastního modulu prostředků.
* `ExtensionAction`: (volitelné, String) určuje, co rozšíření dělá. Platné hodnoty jsou registry, push, Pull, install a Remove. Pokud není zadaný, je ve výchozím nastavení považován za nabízenou akci.
* `NodeConfigurationName`: (volitelné, String) název konfigurace uzlu, která se má použít.
* `RefreshFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) se DSC pokusí získat konfiguraci ze serveru vyžádané replikace.
       Pokud se konfigurace na serveru pro vyžádání liší od aktuální položky na cílovém uzlu, zkopíruje se do úložiště čeká na vyřízení a použije se.
* `ConfigurationMode`: (volitelné, řetězec) určuje, jak má DSC použít konfiguraci. Platné hodnoty jsou ApplyOnly, ApplyAndMonitor a ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (volitelné, int) určuje, jak často (v minutách) DSC ověří, jestli je konfigurace v požadovaném stavu.

> [!NOTE]
> Pokud používáte verzi starší než 2,3, parametr Mode je stejný jako ExtensionAction. Režim se jeví jako přetížený termín. Aby nedocházelo k nejasnostem, použije se ExtensionAction z verze 2,3. Z důvodu zpětné kompatibility podporuje rozšíření jak režim, tak ExtensionAction.
>

### <a name="protected-configuration"></a>Chráněná konfigurace

Tady jsou všechny podporované parametry chráněné konfigurace:

* `StorageAccountName`: (volitelné, řetězec) název účtu úložiště, který obsahuje soubor.
* `StorageAccountKey`: (volitelné, String) klíč účtu úložiště, který obsahuje soubor
* `RegistrationUrl`: (volitelné, String) adresa URL účtu Azure Automation
* `RegistrationKey`: (volitelné, String) přístupový klíč účtu Azure Automation

## <a name="scenarios"></a>Scénáře

### <a name="register-an-azure-automation-account"></a>Registrace účtu Azure Automation

protected.jsna
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.jsna
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru MOF (v účtu Azure Storage) na virtuální počítač

protected.jsna
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsna
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

### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru MOF (ve veřejném úložišti) na virtuální počítač

public.jsna
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

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru meta MOF (v účtu Azure Storage) na virtuální počítač

protected.jsna
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsna
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

public.jsna

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

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor zip v účtu Azure Storage) do virtuálního počítače

protected.jsna

```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsna

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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor zip ve veřejném úložišti) do virtuálního počítače

public.jsna

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

public.jsna

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

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Šablony jsou ideální při nasazení jednoho nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je například připojování k Azure Automation.

Vzorová Správce prostředků šablona je [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) a [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Další informace o šabloně Azure Resource Manager naleznete v tématu [authoring Azure Resource Manager Templates](../../azure-resource-manager/templates/template-syntax.md).

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

### <a name="use-azure-cliazure-cli"></a>Použití [Azure CLI] [Azure-CLI]

Před nasazením rozšíření DSCForLinux nakonfigurujte `public.json` a `protected.json` podle různých scénářů v oddílu 3.

#### <a name="classic"></a>Klasický

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Klasický režim nasazení se taky označuje jako režim správy služeb Azure. Můžete na něj přejít spuštěním:

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

#### <a name="resource-manager"></a>Resource Manager

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
> V režimu Azure Resource Manager `azure vm extension list` není nyní k dispozici.
>

### <a name="use-azure-powershellazure-powershell"></a>Použití [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Klasický

Můžete se přihlásit ke svému účtu Azure v režimu správy služeb Azure spuštěním:

```powershell
Add-AzureAccount
```

A nasaďte rozšíření DSCForLinux spuštěním:

```powershell
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Změňte obsah $privateConfig a $publicConfig podle různých scénářů v předchozí části.

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

```powershell
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Můžete se přihlásit ke svému účtu Azure v režimu Azure Resource Manager spuštěním:

```powershell
Login-AzAccount
```

Další informace o tom, jak používat Azure PowerShell s Azure Resource Manager, najdete v tématu [Správa prostředků Azure pomocí Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Rozšíření DSCForLinux můžete nasadit spuštěním:

```powershell
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Změňte obsah $privateConfig a $publicConfig podle různých scénářů v předchozí části.

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

```powershell
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal a pomocí rozhraní příkazového řádku Azure CLI. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření se zaznamená do následujícího souboru:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kód chyby: 51 představuje nepodporovanou distribuci nebo nepodporovanou akci rozšíření.
V některých případech se rozšíření DSC v systému Linux nedokáže nainstalovat OMI, pokud v počítači už existuje novější verze OMI. [chybová odpověď: (000003) downgrade není povolená.]

### <a name="support"></a>Podpora

Pokud potřebujete další podrobnější informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a Stack Overflow fórech](https://azure.microsoft.com/support/community/). Případně můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**. Informace o použití podpory Azure najdete v tématu [Nejčastější dotazy k podpoře pro Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

Další informace o rozšířeních najdete v tématu [rozšíření virtuálních počítačů a funkce pro Linux](features-linux.md).
