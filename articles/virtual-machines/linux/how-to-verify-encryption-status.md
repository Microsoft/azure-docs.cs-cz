---
title: Jak ověřit stav šifrování pro Linux
description: Tento článek obsahuje pokyny k ověření stavu šifrování z platformy a úrovně operačního režimu.
author: kailashmsft
ms.service: security
ms.topic: article
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18
ms.openlocfilehash: 0aaa32c46d915eafffcfac9d95cfdd3a24d4086d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123429"
---
# <a name="how-to-verify-encryption-status-for-linux"></a>Jak ověřit stav šifrování pro Linux 

**Tento scénář platí pro rozšíření ade dual pass a single-pass.**  
Tento obor dokumentu je ověření stavu šifrování virtuálního počítače pomocí různých metod.

### <a name="environment"></a>Prostředí

- Linuxové distribuce

### <a name="procedure"></a>Postup

Virtuální počítač byl zašifrován pomocí dvouprůchodového nebo jednoho průchodu.

Stav šifrování lze ověřit během nebo po šifrování pomocí různých metod.

>[!NOTE] 
>V celém dokumentu používáme proměnné, podle toho nahraďte hodnoty.

### <a name="verification"></a>Ověření

Ověření lze provést z portálu, Prostředí PowerShell, AZ CLI a nebo ze strany operačního systému virtuálního montu. 

Toto ověření lze provést kontrolou disků připojených k určitému virtuálnímu počítače. 

Nebo dotazem na nastavení šifrování na každém jednotlivém disku, zda je disk připojen nebo nepřipojený.

Pod různými metodami validace:

## <a name="using-the-portal"></a>Používání portálu

Ověřte stav šifrování kontrolou části rozšíření na webu Azure Portal.

V části **Rozšíření** uvidíte rozšíření ADE uvedené. 

Klikněte na něj a podívejte se na **stavovou zprávu**, bude to znamenat aktuální stav šifrování:

![Kontrola portálu číslo 1](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

V seznamu rozšíření se zobrazí odpovídající verze rozšíření ADE. Verze 0.x odpovídá ADE Dual-Pass a verze 1.x odpovídá ADE Single-Pass.

Můžete získat další podrobnosti kliknutím na rozšíření a pak na *Zobrazit podrobný stav*.

Podrobnější stav procesu šifrování uvidíte ve formátu json:

![Kontrola portálu číslo 2](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Kontrola portálu číslo 3](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Dalším způsobem ověření stavu šifrování je zobrazení části **Disky.**

![Kontrola portálu číslo 4](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Tento stav znamená, že disky mají nastavení šifrování razítkem, ale ne, že byly skutečně šifrovány na úrovni operačního systému. Záměrné, disky dostat razítkem první a šifrované později. Pokud se proces šifrování nezdaří, disky mohou skončit orazítkované, ale ne zašifrované. Chcete-li potvrdit, zda jsou disky skutečně šifrované, můžete zkontrolovat šifrování každého disku na úrovni operačního systému.

## <a name="using-powershell"></a>Pomocí prostředí PowerShell

Obecný stav **šifrování** šifrovaného virtuálního mísy můžete ověřit pomocí následujících příkazů prostředí PowerShell:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![zkontrolovat PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Nastavení šifrování můžete zachytit z každého jednotlivého disku pomocí následujících příkazů prostředí PowerShell:

### <a name="single-pass"></a>Jednoprůchodový průchod
Pokud single-pass, nastavení šifrování jsou razítko na každém z disků (OS a data), můžete zachytit nastavení šifrování disku operačního systému v jednom průchodu takto:

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
![Ověření jednoho průchodu operačního příkazu 01](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Pokud na disku není vyraženo nastavení šifrování, bude výstup prázdný, jak je znázorněno níže:

![Nastavení šifrování operačního režimu 2](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Nastavení šifrování datových disků:

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
![Ověřit data jeden ps 001](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Dvouprůchodový průchod
V Dual Pass nastavení šifrování jsou razítkem v modelu virtuálního počítače a ne na každém jednotlivém disku.

Chcete-li ověřit, zda byla nastavení šifrování označena dvěma průchody, můžete použít následující příkazy:

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
![Ověření dvouprůchodového Prostředí PowerShell 1](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>Nepřipojené disky

Zkontrolujte nastavení šifrování pro disky, které nejsou připojené k virtuálnímu počítače.

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

**Stav obecného** šifrování šifrovaného virtuálního virtuálního soudu můžete ověřit pomocí následujících příkazů AZ CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Ověření obecného použití rozhraní příkazu příkazu ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Jeden průchod
Nastavení šifrování můžete ověřit z každého jednotlivého disku pomocí následujících příkazů Příkazového příkazu AZ:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Nastavení šifrování dat](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> V případě, že disk nemá orazítkované nastavení šifrování, bude zobrazen jako "Disk není šifrován"

Podrobné nastavení stavu a šifrování:

Disk operačního systému:

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

![Jedno cli dat ](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Dvojitý průchod

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Ověření obecného duálního použití rozhraní PŘÍKAZOVÉHO PŘÍKAZU: ](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png) Můžete také zkontrolovat nastavení šifrování na profilu úložiště modelu virtuálního počítače na disku operačního systému:

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

![Ověření duálního profilu virtuálního vana pomocí rozhraní PŘÍKAZOVÉHO PŘÍKAZU ](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>Nepřipojené disky

Zkontrolujte nastavení šifrování pro disky, které nejsou připojené k virtuálnímu počítače.

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

Nespravované disky jsou soubory Virtuálního pevného disku, které se ukládají jako objekty BLOB stránky v účtech úložiště Azure.

Chcete-li získat podrobnosti o konkrétním disku, musíte zadat:

ID účtu úložiště, který disk obsahuje.
Připojovací řetězec pro tento konkrétní účet úložiště.
Název kontejneru, který ukládá disk.
Název disku.

Tento příkaz obsahuje seznam všech ID pro všechny účty úložiště:

```bash
az storage account list --query [].[id] -o tsv
```
ID účtu úložiště jsou uvedeny v následujícím formuláři:

/subscriptions/\<id předplatného>/resourceGroups/\<název skupiny prostředků>/providers/Microsoft.Storage/storageAccounts/\<název účtu úložiště>

Vyberte příslušné ID a uložte jej na proměnnou:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```
Připojovací řetězec

Tento příkaz získá připojovací řetězec pro jeden konkrétní účet úložiště a uloží jej na proměnnou:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Název kontejneru.

Následující příkaz uvádí všechny kontejnery pod účtem úložiště:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontejner používaný pro disky se obvykle nazývá "vhds"

Uložení názvu kontejneru na proměnnou 
```bash
ContainerName="name of the container"
```

Název disku.

Pomocí tohoto příkazu můžete vypsat všechny objekty BLOB v konkrétním kontejneru.
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Vyberte disk, na který chcete zadat dotaz, a uložit jeho název do proměnné.
```bash
DiskName="diskname.vhd"
```
Dotaz na nastavení šifrování disku
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="from-the-os"></a>Z operačního es
Ověřit, zda jsou oddíly datového disku šifrované (a disk operačního systému není)

Když je oddíl/disk zašifrován, zobrazí se jako typ **krypty,** když není zašifrován, zobrazí se jako typ **součást/disk**

``` bash
lsblk
```

![Vrstva Krypty operačního systému ](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Další podrobnosti můžete získat pomocí následující varianty "lsblk". 

Zobrazí se vrstva typu **krypty,** která je připojena rozšířením.

Následující příklad ukazuje logické svazky a normální disky s "**kryptoLUKS\_FSTYPE**".

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Os Krypta vrstva 2](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Jako další krok můžete také ověřit, zda je na datovém disku načteny nějaké klíče.

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

A která dm zařízení jsou uvedena jako krypta

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
