---
title: Ověřte stav šifrování pro Linux-Azure Disk Encryption
description: Tento článek poskytuje pokyny k ověření stavu šifrování z úrovní platformy a operačního systému.
author: kailashmsft
ms.service: security
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e39a230c71cf48422220768adfa8de91cbaa6692
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89072838"
---
# <a name="verify-encryption-status-for-linux"></a>Ověření stavu šifrování pro Linux 

Tento článek popisuje, jak ověřit stav šifrování virtuálního počítače pomocí různých metod: Azure Portal, PowerShellu, rozhraní příkazového řádku Azure nebo operačního systému virtuálního počítače (VM). 

Stav šifrování můžete ověřit během nebo po šifrování, a to buď:

- Kontrolují se disky připojené ke konkrétnímu virtuálnímu počítači. 
- Probíhá dotazování na nastavení šifrování na jednotlivých discích bez ohledu na to, jestli je disk připojený nebo nepřipojený.

Tento scénář se vztahuje na Azure Disk Encryption rozšíření pro duální průchod a jedno předání. Distribuce systému Linux jsou jediným prostředím tohoto scénáře.

>[!NOTE] 
>V celém článku používáme proměnné. Nahraďte hodnoty odpovídajícím způsobem.

## <a name="portal"></a>Portál

V Azure Portal v části **rozšíření** vyberte v seznamu rozšíření Azure Disk Encryption. Stav zprávy o **stavu** označuje aktuální stav šifrování:

![Stavová zpráva kontroly portálu se zvýrazněným stavem, verzí a stavovou zprávou](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

V seznamu rozšíření se zobrazí odpovídající verze rozšíření Azure Disk Encryption. Verze 0. x odpovídá Azure Disk Encryption duálnímu průchodu a verze 1. x odpovídá Azure Disk Encryption jedné Pass.

Další podrobnosti můžete získat tak, že vyberete rozšíření a pak vyberete **zobrazit podrobný stav**. Podrobný stav procesu šifrování je zobrazen ve formátu JSON.

![Na portálu zkontrolujte zvýrazněný odkaz zobrazit podrobný stav](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![Podrobný stav ve formátu JSON](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

Dalším způsobem, jak ověřit stav šifrování, je prohlédnout si část **Nastavení disku** .

![Stav šifrování disku s operačním systémem a datových disků](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> Tento stav znamená, že disky mají nastavené šifrování, a to bez toho, aby byly ve skutečnosti zašifrované na úrovni operačního systému.
>
> Podle návrhu jsou disky označeny jako první a šifrované později. Pokud proces šifrování selhává, disky se můžou zaregistrovat, ale nešifrují se. 
>
> Pokud chcete potvrdit, jestli jsou disky skutečně šifrované, můžete na úrovni operačního systému dvakrát zkontrolovat šifrování každého disku.

## <a name="powershell"></a>PowerShell

*Obecný* stav šifrování ŠIFROVANÉHO virtuálního počítače můžete ověřit pomocí následujících příkazů PowerShellu:

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![Obecný stav šifrování v PowerShellu](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

Nastavení šifrování můžete zachytit z každého disku pomocí následujících příkazů PowerShellu.

### <a name="single-pass"></a>Single Pass
V jednom průchodu se nastavení šifrování vyplní na všech discích (operační systém a data). Nastavení šifrování pro disk s operačním systémem můžete zachytit v jednom průchodu následujícím způsobem:

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
![Nastavení šifrování pro disk s operačním systémem](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

Pokud disk nemá natištěné nastavení šifrování, bude výstup prázdný:

![Prázdný výstup](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

Pro zachycení nastavení šifrování datových disků použijte následující příkazy:

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
![Nastavení šifrování datových disků](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>Duální průchod
Ve dvojím průchodu se nastavení šifrování vyplní v modelu virtuálního počítače, a ne na každém jednotlivém disku.

Chcete-li ověřit, zda byla nastavení šifrování označena dvojím průchodem, použijte následující příkazy:

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
![Nastavení šifrování ve dvojím průchodu](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

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
## <a name="azure-cli"></a>Azure CLI

*Obecný* stav šifrování ŠIFROVANÉHO virtuálního počítače můžete ověřit pomocí následujících příkazů Azure CLI:

```bash
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Obecný stav šifrování z Azure CLI ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>Single Pass
Nastavení šifrování pro každý disk můžete ověřit pomocí následujících příkazů Azure CLI:

```bash
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![Nastavení šifrování dat](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> Pokud disk nemá natištěné nastavení šifrování, zobrazí se textový disk není **zašifrovaný**.

Podrobné nastavení stavu a šifrování získáte pomocí následujících příkazů.

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

![Podrobné nastavení stavu a šifrování pro disk s operačním systémem](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

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

![Podrobné nastavení stavu a šifrování datových disků](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>Duální průchod

``` bash
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Obecné nastavení šifrování pro duální průchod prostřednictvím rozhraní příkazového řádku Azure](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

Můžete také ověřit nastavení šifrování pro profil úložiště modelu virtuálního počítače na disku s operačním systémem:

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

![Profil virtuálního počítače pro duální průchod prostřednictvím rozhraní příkazového řádku Azure](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

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

- ID účtu úložiště, který obsahuje disk
- Připojovací řetězec pro daný účet úložiště.
- Název kontejneru, který ukládá disk.
- Název disku.

Tento příkaz vypíše všechna ID pro všechny vaše účty úložiště:

```bash
az storage account list --query [].[id] -o tsv
```
ID účtu úložiště jsou uvedená v následujícím tvaru:

/Subscriptions/ \<subscription id> /ResourceGroups/ \<resource group name> /providers/Microsoft.Storage/storageAccounts/\<storage account name>

Vyberte odpovídající ID a uložte ho do proměnné:
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

Tento příkaz načte připojovací řetězec pro jeden konkrétní účet úložiště a uloží ho do proměnné:

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

Následující příkaz vypíše všechny kontejnery v rámci účtu úložiště:
```bash
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
Kontejner používaný pro disky se obvykle nazývá "VHD".

Uložte název kontejneru do proměnné: 
```bash
ContainerName="name of the container"
```

Tento příkaz slouží k vypsání všech objektů BLOB v konkrétním kontejneru:
```bash 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
Vyberte disk, na který chcete zadat dotaz, a uložte jeho název do proměnné:
```bash
DiskName="diskname.vhd"
```
Dotaz na nastavení šifrování disku:
```bash
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>Operační systém
Ověří, jestli jsou oddíly datových disků šifrované (a disk s operačním systémem není).

Když je oddíl nebo disk zašifrovaný, zobrazuje se jako **nešifrovaný** typ. Pokud není šifrovaný, zobrazí se jako typ **součásti nebo disku** .

``` bash
lsblk
```

![Vrstva kryptografie operačního systému pro oddíl](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

Další podrobnosti můžete získat pomocí následujících **lsblk** variant. 

Zobrazí se vrstva typu **nešifrovaných** typů, která je připojena rozšířením. Následující příklad ukazuje logické svazky a běžné disky s **kryptografickým \_ LUKS FSTYPE**.

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![Vrstva kryptografie operačního systému pro logické svazky a běžné disky](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

Jako další krok můžete ověřit, jestli datový disk obsahuje nějaké klíče:

``` bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

``` bash
cryptsetup luksDump /dev/sdd1
```

A můžete zjistit, která zařízení **DM** jsou uvedena jako **kryptografie**:

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
