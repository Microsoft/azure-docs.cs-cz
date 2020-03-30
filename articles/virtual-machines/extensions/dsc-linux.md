---
title: Rozšíření Azure DSC pro Linux
description: Nainstaluje balíčky OMI a DSC, aby bylo možné nakonfigurovat virtuální počítač Azure Linux pomocí konfigurace požadovaného stavu.
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
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250618"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Rozšíření DSC pro Linux (Microsoft.OSTCExtensions.DSCForLinux)

Konfigurace požadovaného stavu (DSC) je platforma pro správu, kterou můžete použít ke správě it a vývojové infrastruktury s konfigurací jako kódem.

> [!NOTE]
> Rozšíření DSC pro Linux a [rozšíření virtuálního počítače Azure Monitor pro Linux](/azure/virtual-machines/extensions/oms-linux) aktuálně představují konflikt a nejsou podporovány v konfiguraci vedle sebe. Nepoužívejte dvě řešení společně na stejném virtuálním počítači.

Rozšíření DSCForLinux je publikováno a podporováno společností Microsoft. Rozšíření nainstaluje agenta OMI a DSC na virtuální počítače Azure. Rozšíření DSC můžete také provést následující akce:


- Zaregistrujte virtuální počítač s Linuxem do účtu Azure Automation pro vyžádat konfigurace ze služby Azure Automation (Register ExtensionAction).
- Push MOF konfigurace na virtuální počítač s Linuxem (Push ExtensionAction).
- Použití meta MOF konfigurace pro virtuální počítač s Linuxem nakonfigurovat server pro vyžádat pro vyžádat konfiguraci uzlu (Pull ExtensionAction).
- Nainstalujte vlastní moduly DSC do virtuálního počítače SIP (Install ExtensionAction).
- Odeberte vlastní moduly DSC z virtuálního počítače SIB (Odebrat rozšířeníAkce).

 

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření DSC Linux podporuje všechny [linuxové distribuce schválené v Azure](/azure/virtual-machines/linux/endorsed-distros) s výjimkou:

| Distribuce | Version |
|---|---|
| Debian | Všechny verze |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Připojení k internetu

Rozšíření DSCForLinux vyžaduje, aby byl cílový virtuální počítač připojen k internetu. Například register rozšíření vyžaduje připojení ke službě Automatizace. Pro další akce, jako je pull, Pull, Install vyžaduje připojení k Azure Storage a GitHub. Záleží na nastavení poskytnutém zákazníkem.

## <a name="extension-schema"></a>Schéma rozšíření

### <a name="public-configuration"></a>Veřejná konfigurace

Zde jsou všechny podporované parametry veřejné konfigurace:

* `FileUri`: (volitelné, řetězec) uri souboru MOF, meta MOF souboru nebo vlastní soubor zip prostředku.
* `ResourceName`: (volitelné, řetězec) Název modulu vlastního prostředku.
* `ExtensionAction`: (volitelné, řetězec) Určuje, co rozšíření dělá. Platné hodnoty jsou Register, Push, Pull, Install a Remove. Pokud není zadán, je ve výchozím nastavení považován za nabízenou akci.
* `NodeConfigurationName`: (volitelné, řetězec) Název konfigurace uzlu použít.
* `RefreshFrequencyMins`: (volitelné, int) Určuje, jak často (v minutách) dsc pokusí získat konfiguraci ze serveru vyžádat. 
       Pokud se konfigurace na serveru vyžádat se liší od aktuální konfigurace v cílovém uzlu, zkopíruje se do čekajícího úložiště a použije se.
* `ConfigurationMode`: (volitelné, řetězec) Určuje, jak má DSC použít konfiguraci. Platné hodnoty jsou ApplyOnly, ApplyAndMonitor a ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (volitelné, int) Určuje, jak často (v minutách) DSC zajišťuje, že konfigurace je v požadovaném stavu.

> [!NOTE]
> Pokud používáte verzi starší než 2.3, parametr režimu je stejný jako ExtensionAction. Zdá se, že režim je přetížený termín. Aby se zabránilo nejasnostem, ExtensionAction se používá od verze 2.3 kupředu. Pro zpětnou kompatibilitu rozšíření podporuje režim a ExtensionAction. 
>

### <a name="protected-configuration"></a>Chráněná konfigurace

Zde jsou všechny podporované chráněné konfigurační parametry:

* `StorageAccountName`: (volitelné, řetězec) Název účtu úložiště, který obsahuje soubor
* `StorageAccountKey`: (volitelné, řetězec) Klíč účtu úložiště, který obsahuje soubor
* `RegistrationUrl`: (volitelné, řetězec) Adresa URL účtu Azure Automation
* `RegistrationKey`: (volitelné, řetězec) Přístupový klíč účtu Azure Automation


## <a name="scenarios"></a>Scénáře

### <a name="register-an-azure-automation-account"></a>Registrace účtu Azure Automation
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru MOF (v účtu úložiště Azure) na virtuální počítač

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru MOF (ve veřejném úložišti) na virtuální ms

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Použití konfiguračního souboru meta MOF (v účtu úložiště Azure) na virtuální počítač

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Použití konfiguračního souboru meta MOF (ve veřejném úložišti) na virtuální m
public.json
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

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor zip v účtu úložiště Azure) do virtuálního počítače
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalace vlastního modulu prostředků (soubor zip ve veřejném úložišti) do virtuálního virtuálního zařízení
public.json
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Odebrání vlastního modulu prostředků z virtuálního mísy
public.json
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

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Šablony jsou ideální, když nasadíte jeden nebo více virtuálních počítačů, které vyžadují konfiguraci po nasazení, jako je například připojení do Azure Automation. 

Ukázka šablony Správce prostředků je [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) a [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Další informace o šabloně Azure Resource Manager najdete v [tématu Vytváření šablon Azure Resource Manageru](../../azure-resource-manager/templates/template-syntax.md).


## <a name="azure-cli-deployment"></a>Nasazení azure cli

### <a name="use-azure-cliazure-cli"></a>Použití [Azure CLI][azure-cli]
Před nasazením rozšíření DSCForLinux `public.json` nakonfigurujte si a `protected.json` podle různých scénářů v části 3.

#### <a name="classic"></a>Classic

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Klasický režim nasazení se také nazývá režim správy služeb Azure. Můžete přepnout na něj spuštěním:
```
$ azure config mode asm
```

Rozšíření DSCForLinux můžete nasadit spuštěním:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Chcete-li se dozvědět nejnovější dostupnou verzi rozšíření, spusťte:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Do režimu Azure Resource Manageru můžete přepnout spuštěním:
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
> V režimu Azure `azure vm extension list` Resource Manager, není k dispozici pro tuto chvíli.
>

### <a name="use-azure-powershellazure-powershell"></a>Použití [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Classic

Ke svému účtu Azure se můžete přihlásit v režimu Správy služeb Azure spuštěním:

```powershell>
Add-AzureAccount
```

A nasadit rozšíření DSCForLinux spuštěním:

```powershell>
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

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Ke svému účtu Azure se můžete přihlásit v režimu Azure Resource Manager spuštěním:

```powershell>
Login-AzAccount
```

Další informace o tom, jak používat Azure PowerShell ve Správci prostředků Azure, najdete [v tématu Správa prostředků Azure pomocí Azure PowerShellu](../../azure-resource-manager/management/manage-resources-powershell.md).

Rozšíření DSCForLinux můžete nasadit spuštěním:

```powershell>
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

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Poradce při potížích a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření lze načíst z portálu Azure a pomocí azure cli. Chcete-li zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz pomocí příkazu Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Výstup spuštění rozšíření je zaznamenán do následujícího souboru:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Kód chyby: 51 představuje buď nepodporovanou distribuci, nebo nepodporovanou akci rozšíření.
V některých případech rozšíření DSC Linux nenainstaluje OMI, pokud v počítači již existuje vyšší verze OMI. [Chyba odpověď: (000003)Downgrade není povoleno]



### <a name="support"></a>Podpora

Pokud potřebujete další pomoc v libovolném bodě v tomto článku, obraťte se na odborníky Azure na [Fóra MSDN Azure a přetečení zásobníku](https://azure.microsoft.com/support/community/). Případně můžete soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte Získat **podporu**. Informace o používání podpory Azure načtete v [nejčastějších dotazech k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky
Další informace o rozšířeních najdete [v tématu Rozšíření virtuálních strojů a funkce pro Linux](features-linux.md).
