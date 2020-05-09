---
title: Jak ověřit stav šifrování pro Linux
description: Tento článek poskytuje pokyny k ověření stavu šifrování z platformy a na úrovni operačního systému.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123429"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Jak ověřit stav šifrování pro Linux 

**Tento scénář platí pro rozšíření ADE Dual-Pass a Single Pass.**  
Tento obor dokumentů slouží k ověření stavu šifrování virtuálního počítače pomocí různých metod.

### <a name="environment"></a>Prostředí

- Distribuce systému Linux

### <a name="procedure"></a>Postup

Virtuální počítač je zašifrovaný pomocí dvojího nebo jednoduchého průchodu.

Stav šifrování lze ověřit během nebo po šifrování pomocí různých metod.

>[!NOTE] 
>V celém dokumentu používáme proměnné, hodnoty proto nahraďte odpovídajícím způsobem.

### <a name="verification"></a>Ověření

Ověření se dá provést z portálu, PowerShellu, AZ CLI a nebo z na straně operačního systému virtuálního počítače. 

Toto ověření se dá udělat tak, že zkontrolujete disky připojené ke konkrétnímu virtuálnímu počítači. 

Nebo pomocí dotazu na nastavení šifrování na jednotlivých discích, ať už je disk připojený nebo nepřipojený.

Pod různými metodami ověřování:

## <a name="using-the-portal"></a>Používání portálu

Ověřte stav šifrování zaškrtnutím části rozšíření na Azure Portal.

V části **rozšíření** se zobrazí uvedená přípona ADE. 

Klikněte na něj a podívejte se na **stavovou zprávu**, která bude označovat aktuální stav šifrování:

![Číslo kontroly portálu 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

V seznamu rozšíření se zobrazí odpovídající verze rozšíření ADE. Verze 0. x odpovídá ADE Dual-Pass a verze 1. x odpovídá jedinému průchodu ADE.

Můžete získat další podrobnosti kliknutím na rozšíření a pak při *zobrazení podrobného stavu*.

Ve formátu JSON se zobrazí podrobnější stav procesu šifrování:

![Číslo kontroly portálu 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Číslo kontroly portálu 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Dalším způsobem, jak ověřit stav šifrování, je prohlédnout si oddíl **disky** .

![Číslo kontroly portálu 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Tento stav znamená, že disky mají nastavené šifrování, ale nejsou ve skutečnosti zašifrované na úrovni operačního systému. Podle návrhu se disky nejprve zašifrují a šifrují později. Pokud proces šifrování selhává, disky se můžou zaregistrovat, ale nešifrují se. Pokud chcete potvrdit, jestli jsou disky skutečně zašifrované, můžete dvakrát zkontrolovat šifrování každého disku na úrovni operačního systému.

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

**Obecný** stav šifrování ŠIFROVANÉHO virtuálního počítače můžete ověřit pomocí následujících příkazů PowerShellu:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![ověřit PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Nastavení šifrování můžete zachytit z každého jednotlivého disku pomocí následujících příkazů PowerShellu:

### <a name="single-pass"></a>Jednoduché předání
Pokud je jedno předání, nastavení šifrování je na každém disku (operačním systému a datech) razítko. nastavení šifrování disku operačního systému můžete zachytit v jednom průchodu následujícím způsobem:

``` powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![Ověřit jeden průchod operačním systémem 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Pokud disk nemá natištěné nastavení šifrování, bude výstup prázdný, jak je znázorněno níže:

![Nastavení šifrování operačního systému 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Zachytávání nastavení šifrování datových disků:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![Ověřit data v jednom PS 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dvojí průchod
Ve duálním průchodu jsou nastavení šifrování označená v modelu virtuálního počítače, a ne na každém jednotlivém disku.

Chcete-li ověřit, zda byla nastavení šifrování označena dvojím průchodem, můžete použít následující příkazy:

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![Ověření duálního předávacího prostředí PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nepřipojené disky

Ověřte nastavení šifrování pro disky, které nejsou připojené k virtuálnímu počítači.

### <a name="managed-disks"></a>Spravované disky
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="using-az-cli"></a>Použití AZ CLI

**Obecný** stav šifrování ŠIFROVANÉHO virtuálního počítače můžete ověřit pomocí následujících příkazů AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Ověření obecného použití rozhraní příkazového řádku ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Single Pass
Nastavení šifrování můžete z každého jednotlivého disku ověřit pomocí následujících příkazů AZ CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Nastavení šifrování dat](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> V případě, že se na disku nezobrazuje razítko s nastavením šifrování, bude zobrazená jako "disk není zašifrovaný".

Podrobné nastavení stavu a šifrování:

Disk s operačním systémem:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OSSingleCLI](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

Datové disky:

```bash
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Data Single CLI ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Duální průchod

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Ověření obecného duálního použití ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) rozhraní příkazového řádku můžete také zkontrolovat nastavení šifrování na profilu úložiště modelu virtuálního počítače na disku s operačním systémem:

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Ověření duálního profilu virtuálního počítače pomocí rozhraní příkazového řádku ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nepřipojené disky

Ověřte nastavení šifrování pro disky, které nejsou připojené k virtuálnímu počítači.

### <a name="managed-disks"></a>Spravované disky

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>Nespravované disky

Nespravované disky jsou soubory VHD, které se ukládají jako objekty blob stránky v účtech úložiště Azure.

Chcete-li získat podrobnosti o konkrétním disku, je třeba zadat následující:

ID účtu úložiště, který obsahuje disk
Připojovací řetězec pro daný účet úložiště.
Název kontejneru, který ukládá disk.
Název disku.

Tento příkaz vypíše všechna ID pro všechny vaše účty úložiště:

```bash
az storage account list --query [].[id] -o tsv
```
ID účtu úložiště jsou uvedená v následujícím tvaru:

ID\<předplatného/Subscriptions/\<> název skupiny prostředků/ResourceGroups/\<> název účtu úložiště/Providers/Microsoft.Storage/storageaccounts/>

Vyberte odpovídající ID a uložte ho do proměnné:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Připojovací řetězec

Tento příkaz načte připojovací řetězec pro jeden konkrétní účet úložiště a uloží ho do proměnné:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Název kontejneru.

Následující příkaz vypíše všechny kontejnery v rámci účtu úložiště:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontejner používaný pro disky se obvykle nazývá "VHD".

Uložit název kontejneru pro proměnnou 
```bash
ContainerName="name of the container"
```

Název disku.

Tento příkaz slouží k vypsání všech objektů BLOB v konkrétním kontejneru.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Vyberte disk, na který chcete zadat dotaz, a uložte jeho název na proměnnou.
```bash
DiskName="diskname.vhd"
```
Dotaz na nastavení šifrování disku
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Z operačního systému
Ověří, jestli jsou oddíly datových disků šifrované (a disk s operačním systémem není).

Když je oddíl nebo disk zašifrovaný, zobrazuje se jako typ **kryptografie** , pokud není zašifrovaný, zobrazuje se jako typ **součásti nebo disku** .

``` bash
lsblk
```

![Vrstva kryptografie operačního systému ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Další podrobnosti můžete získat pomocí následující varianty "lsblk". 

Zobrazí se vrstva typu **nešifrovaných** typů, která je připojena rozšířením.

Následující příklad ukazuje logické svazky a běžné disky s "**kryptografickým\_LUKS FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Nešifrovaný vrstva OS 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Jako další krok můžete také ověřit, jestli datový disk obsahuje nějaké klíče.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

A která zařízení DM jsou uvedena jako nešifrovaný

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
